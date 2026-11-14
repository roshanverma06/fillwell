# Fillwell: Technical Design Document (MVP / V1)

| | |
|---|---|
| **Status** | Draft v0.1, for design review |
| **Owner** | Tech lead (assign) |
| **Reviewers** | Extension lead, backend lead, AI lead |
| **Last updated** | November 14, 2026 |
| **Related** | `product/prd.md`, `design/threat-model.md`, `design/privacy-data-handling.md`, `adr/*` |

---

## 1. Overview

Fillwell is a Chrome extension backed by an API service. The extension reads application forms, asks the backend for a **fill plan** (what goes in each field, from which source, with what confidence), shows the plan for review, and then writes the values into the page. The backend stores the user's profile, resumes, answer bank and application log. It runs matching (embeddings) and grounded answer generation (LLM).

## 2. Architecture

```
┌──────────────────────────── Chrome (user's browser) ────────────────────────────┐
│                                                                                 │
│  Job application page (Greenhouse / Lever / Ashby)                              │
│   └─ Content script                                                             │
│       ├─ Site adapter (per ATS)  → extracts FieldDescriptors                    │
│       ├─ Filler                  → writes values (React-safe events)            │
│       ├─ Upload watcher          → notices resume file attached to the form     │
│       └─ Field badges (Shadow DOM) → source / confidence tags                   │
│                │ chrome.runtime messages                                        │
│  Service worker (background)                                                    │
│   ├─ Auth (Google sign-in → backend tokens)                                     │
│   ├─ API client                                                                 │
│   └─ Session store (chrome.storage.session: per-application state)            │
│                │                                                                │
│  Side panel (React)                                                             │
│   ├─ Fill preview & approve                                                     │
│   ├─ "New question" prompts                                                     │
│   ├─ AI draft review (with citations)                                           │
│   └─ Profile / answer bank / settings screens                                   │
└────────────────│────────────────────────────────────────────────────────────────┘
                 │ HTTPS (JWT)
┌────────────────▼──────────── Backend (AWS) ─────────────────────────────────────┐
│  FastAPI service                                                                │
│   ├─ /auth        ├─ /profile      ├─ /resumes     ├─ /answers                  │
│   ├─ /fill/plan   ├─ /generate     ├─ /applications├─ /me/export, /me (delete)  │
│   │                                                                             │
│   ├─ Matching engine   (normalize → exact hash → vector search → thresholds)   │
│   ├─ Field classifier  (rules + keyword lists; LLM fallback later)              │
│   ├─ Resume parser     (text → structured JSON via LLM, schema-validated)       │
│   ├─ Generator         (retrieve facts → prompt → citations → validate)         │
│   └─ Sensitive guard   (removes sensitive fields before any LLM call)           │
│                                                                                 │
│  PostgreSQL 16 + pgvector (RDS)   S3 (resume files, encrypted)                  │
│  Secrets Manager                  CloudWatch + Sentry                           │
└─────────────────────────────────│───────────────────────────────────────────────┘
                                  │
                  LLM API (generation, parsing)   Embedding model (in-service)
```

## 3. Tech stack

| Layer | Choice | ADR |
|---|---|---|
| Extension | TypeScript, Manifest V3, **WXT** framework, React (side panel), Zod | ADR-001 |
| Backend | Python 3.12, **FastAPI**, Pydantic v2, SQLAlchemy 2 + Alembic, uv | ADR-002 |
| Database | **PostgreSQL 16 + pgvector** | ADR-003 |
| AI | LLM API behind a provider interface (Claude by default); open-source embedding model run in the backend | ADR-004 |
| Auth | Google sign-in via `chrome.identity` → backend-issued JWT | ADR-005 |
| Hosting | AWS: App Runner (API), RDS Postgres, S3, Secrets Manager | ADR-006 |
| Repo | Monorepo: `apps/extension`, `apps/api`, `packages/shared-schemas`, `docs/` | ADR-007 |
| CI/CD | GitHub Actions: lint, type check, tests, build; auto-deploy to staging on merge to `main` | — |
| Observability | Sentry (extension + API), structured JSON logs (no personal data), Langfuse for LLM tracing | — |

## 4. Core concepts

### 4.1 FieldDescriptor (extension → backend)
Describes a form field **without any user data**.

```ts
type FieldDescriptor = {
  fieldId: string;          // stable id within this page (adapter-generated)
  label: string;            // visible question text, cleaned up
  helperText?: string;      // hint text, placeholder, aria-describedby
  type: "text" | "email" | "tel" | "url" | "number" | "date" | "textarea"
      | "select" | "radio" | "checkbox" | "file";
  options?: string[];       // for select / radio / checkbox
  required: boolean;
  maxLength?: number;
  currentValueState: "empty" | "filled";   // the value itself is never sent
  section?: string;         // e.g. "Voluntary Self-Identification"
};
```

### 4.2 Field classes
Every field is classified into one of these:

| Class | Examples | Source |
|---|---|---|
| `identity` | name, email, phone, address, links | Profile |
| `resume` | employers, titles, dates, education, skills, summary | Precedence: attached resume → default resume → profile |
| `eligibility` | work authorization, sponsorship, relocation, start date | Answer bank / rules (**sensitive**) |
| `eeo` | gender, race, veteran, disability | Rules only (**sensitive**, never sent to LLM) |
| `screening` | "Years of experience with Python?" | Answer bank |
| `descriptive` | "Why do you want to work here?" | Generator (AI) |
| `file` | resume, cover letter upload | User's choice / default |
| `unknown` | anything else | Ask the user |

### 4.3 FillPlan (backend → extension)

```ts
type FillPlanItem = {
  fieldId: string;
  action: "fill" | "suggest" | "ask" | "generate" | "skip";
  value?: string | string[];
  source: { kind: "profile" | "resume" | "answer_bank" | "ai" | "rule";
            ref?: string; label: string };     // e.g. "resume:RoshanVerma_Tesla_DE.pdf"
  confidence?: number;                          // 0..1
  tier?: "green" | "yellow" | "red";
  reason?: string;                              // "94% match to question answered 2026-10-03"
};
```

- `fill`: field empty, high confidence
- `suggest`: field already has a different value; show a suggestion only (FR-2)
- `ask`: no confident match; prompt the user
- `generate`: descriptive field; draft with the AI
- `skip`: user rule or unsupported field

## 5. Key flows

### 5.1 Autofill
1. User opens an application page. The content script finds the matching **site adapter** by URL and DOM signature.
2. The adapter extracts `FieldDescriptor[]` and the **job description text** (from the posting section).
3. The upload watcher checks whether a resume is already attached (§5.3).
4. Service worker → `POST /fill/plan` with `{descriptors, jobContext, applicationResumeId?}`.
5. Backend: classify each field → look up the source by class → match against the answer bank → build the `FillPlan`.
6. The side panel shows the **preview**. The user approves all, edits some, or deselects.
7. The filler writes approved values using React-safe value setting (native setter + `input`/`change`/`blur` events). Badges show source and tier.
8. `ask` items appear as prompts in the side panel → answers saved via `POST /answers` → field filled.
9. `generate` items → `POST /generate` → draft with citations shown → user edits or accepts → filled.
10. The user clicks Submit **themselves**. The extension detects the submission (URL or DOM change) and logs it with `POST /applications`.

### 5.2 Answer matching
```
question text
  → normalize (lowercase, strip punctuation and boilerplate like "please indicate")
  → SHA-256 of normalized text → exact lookup in question_variants
      hit  → confidence 1.0
      miss → embed (384-dim) → pgvector cosine search (top-5, same user)
             → best ≥ 0.90 → green | 0.80–0.90 → yellow | < 0.80 → red (ask)
  → if answer type is choice: map stored answer to this field's options
      (exact → fuzzy token match → LLM fallback, non-sensitive fields only)
  → when the user confirms or edits: store the new wording as a variant of that answer
```

### 5.3 Resume precedence (FR-1)
1. The upload watcher listens for `change` and `drop` events on file inputs and dropzones.
2. On attach: read the `File` in the content script → compute SHA-256 → `POST /resumes` (uploads and parses, deduplicated by hash).
3. The returned `resumeId` is stored in `chrome.storage.session` keyed by the **application key** (ATS + job ID from the URL), so it survives multi-page flows.
4. `/fill/plan` uses `applicationResumeId` for `resume`-class fields; otherwise the user's default resume.
5. Scanned or empty PDF (no text) → fall back to the default resume and say why in the preview.

### 5.4 Grounded generation (FR-7)
1. Pull keywords from the job description (company, role, skills).
2. Retrieve the top-k facts from `knowledge_items` (vector search plus keyword boost) for this user.
3. The **sensitive guard** removes any fact or field tagged sensitive.
4. Prompt (versioned template) includes: question, length limit, job description excerpt (marked as **untrusted data**), numbered facts. The required output is JSON `{answer, citations: [factId…], insufficient_info: bool}`.
5. Validate: schema; every citation exists; within length. On failure, retry once, then return `insufficient_info`.
6. Store in `generations` with prompt version, model, token usage and cost.

## 6. Data model (PostgreSQL)

```sql
users            (id uuid pk, google_sub text unique, email text, created_at, deleted_at)
profiles         (user_id pk/fk, data jsonb, updated_at)            -- identity + structured resume facts
personas         (id, user_id, name, is_default)                     -- one in MVP; many in V2
resumes          (id, user_id, filename, file_sha256, s3_key, parsed jsonb,
                  parse_status, is_default, created_at)              -- unique (user_id, file_sha256)
knowledge_items  (id, user_id, kind, content text, source_ref, sensitive bool,
                  embedding vector(384), created_at)                 -- facts used for grounding
answers          (id, user_id, field_class, canonical_question text, answer_type,
                  value_enc bytea, sensitive bool, expires_at null, version int,
                  created_at, updated_at)
question_variants(id, answer_id fk, text, normalized_sha256, embedding vector(384),
                  site text, created_at)                             -- index: (user_id via answer), hnsw on embedding
sensitive_rules  (id, user_id, field_class, rule jsonb)              -- e.g. {"action":"fixed","value":"Decline to self-identify"}
applications     (id, user_id, ats, company, role, url, application_key,
                  resume_id, jd_text, submitted_at, status)
application_fields(id, application_id, field_label, value_enc bytea,
                  source_kind, source_ref, confidence, user_edited bool)
generations      (id, user_id, application_id, question, prompt_version, model,
                  output text, citations jsonb, insufficient_info bool,
                  final_text text, tokens_in, tokens_out, cost_usd, created_at)
audit_log        (id, user_id, action, target, created_at)          -- exports, deletes, rule changes
```

- `value_enc` columns use application-level encryption (AES-GCM, key from AWS KMS via Secrets Manager).
- All user-owned tables are filtered by `user_id` in a single repository layer. Postgres row-level security is planned for V2.
- Deleting a user cascades to everything and deletes their S3 objects; the deletion is logged in `audit_log` (with no content).

## 7. API (MVP)

All endpoints require `Authorization: Bearer <jwt>` except auth. FastAPI generates the OpenAPI spec at `/docs` (staging only).

| Method | Path | Purpose |
|---|---|---|
| POST | `/auth/google` | Exchange Google token for Fillwell access + refresh JWT |
| POST | `/auth/refresh` | Refresh the access token |
| GET / PUT | `/profile` | Read / update profile |
| POST | `/resumes` | Upload + parse (deduplicated by hash) → `{resumeId, parsed, status}` |
| GET | `/resumes` · PATCH `/resumes/{id}` | List; set default; confirm parsed data |
| POST | `/fill/plan` | Descriptors + job context → `FillPlan` |
| GET | `/answers?query=` | List / search the answer bank |
| POST | `/answers` | Create an answer (from a prompt) with its first variant |
| PATCH / DELETE | `/answers/{id}` | Edit / delete |
| POST | `/answers/{id}/variants` | Link new question wording to an existing answer |
| POST | `/generate` | Grounded draft for a descriptive field |
| POST | `/applications` · GET `/applications` | Log / list applications |
| GET / PUT | `/rules` | Sensitive-field rules |
| GET | `/me/export` | Full JSON export of the user's data |
| DELETE | `/me` | Permanently delete the account and all data |

**Limits:** `/generate` 30/hour/user; `/fill/plan` 120/hour/user; resume parsing 20/day/user.

## 8. Extension design details

- **Permissions:** `storage`, `sidePanel`, `identity`, `activeTab`, `scripting`. Host permissions only for `*.greenhouse.io`, `jobs.lever.co`, `jobs.ashbyhq.com` (more requested optionally per site later). No `<all_urls>`.
- **Site adapters:** a shared interface `detect() / extractFields() / extractJobContext() / fill(fieldId, value) / onSubmit(cb)` with one implementation per ATS, plus a `GenericAdapter` best-effort fallback (V2).
- **Isolation:** Fillwell UI on the page lives inside a closed Shadow DOM; sensitive data is shown only in the side panel (the extension's own page), never in the job site's page.
- **State:** per-application state in `chrome.storage.session` (cleared when the browser closes); auth tokens in `chrome.storage.local`; no profile data is kept in the extension beyond the current session.

## 9. Non-functional requirements

| Area | Target |
|---|---|
| `/fill/plan` latency | p95 < 800 ms (no generation) |
| `/generate` latency | p95 < 8 s |
| Availability (beta) | Best effort; staging and prod separate |
| Cost | < $0.05 LLM cost per application (median) |
| Security | OWASP ASVS L1 basics; see threat model |
| Privacy | No personal data in logs; sensitive fields never reach the LLM |
| Accessibility | Side panel keyboard-navigable; WCAG AA contrast |

## 10. Observability

- **Logs:** JSON with request id, user id hash, endpoint, latency, status. **No values, labels with personal data, or job description text.**
- **Metrics:** fill-plan items by action/tier, user corrections per field class, match tier distribution, generation `insufficient_info` rate, LLM cost per user per day.
- **Tracing:** Langfuse for LLM calls (prompts stored with sensitive data removed).
- **Errors:** Sentry in the extension and API, with personal data scrubbing on.

## 11. Alternatives considered
- **No backend (extension-only):** simpler and more private, but puts the API key in the client, makes cross-device sync and evals harder, and is a weaker production-engineering story. Revisit as L11 (local-only mode).
- **Hosted vector DB (Pinecone):** unnecessary at this scale; pgvector keeps one database (ADR-003).
- **Playwright bot instead of an extension:** would make auto-submit possible, which is out of scope, and needs separate logins.

## 12. Open questions
1. Exact similarity thresholds, decided after the labeled eval set is built (Sprint 3).
2. Whether to run the embedding model in the API container or as a separate service (memory footprint).
3. Production LLM model choice and monthly budget cap.
