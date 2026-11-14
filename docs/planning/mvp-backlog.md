# Fillwell: MVP (V1) Backlog

| | |
|---|---|
| **Status** | Draft v0.1, ready for Sprint 0 planning |
| **Last updated** | November 14, 2026 |

**How to use:** each ticket becomes a GitHub Issue (title = `FW-### Title`) in the Fillwell GitHub Project, labeled with its epic, size and sprint.

**Sizes:** S ≈ ≤ 4 hrs · M ≈ 1–2 days · L ≈ 3–5 days (split anything bigger).

**Owner roles:** `EXT` extension · `API` backend · `AI` matching/LLM · `INFRA` devops · `QA` testing · `PM` product/docs. Map roles to people in Sprint 0.

---

## E0: Foundations (Sprint 0–1)

| ID | Ticket | Size | Owner | Sprint | Acceptance criteria |
|---|---|---|---|---|---|
| FW-001 | Design review of PRD + technical design + threat model | M | PM | S0 | Comments resolved; docs marked "Approved" |
| FW-002 | Create monorepo per ADR-007 | S | INFRA | S0 | `make dev`, `make test`, `make lint` run on a clean clone |
| FW-003 | CI pipeline (lint, type check, tests, path filters, gitleaks, dependency audit) | M | INFRA | S0 | PRs blocked on red CI; secret scan active |
| FW-004 | WXT extension skeleton (service worker, content script, side panel "Hello") | S | EXT | S0 | Loads unpacked in Chrome; hot reload works |
| FW-005 | FastAPI skeleton + `/health` + Dockerfile | S | API | S0 | Container runs locally; `/health` returns 200 |
| FW-006 | Terraform: staging (App Runner, RDS + pgvector, S3, Secrets Manager, budget alert) | L | INFRA | S0 | `terraform apply` creates staging; budget alert email works |
| FW-007 | **Spike:** React-safe value setting on Greenhouse + Lever custom selects | M | EXT | S0 | Written findings + a working proof of concept |
| FW-008 | **Spike:** embedding model in-process (memory, latency) | M | AI | S0 | Decision recorded in ADR-004 (Accepted) |
| FW-009 | Collect 30+ form HTML snapshots (10 each: Greenhouse, Lever, Ashby) into `fixtures/forms/` | M | QA | S0 | Snapshots + expected field lists (JSON) committed; personal data removed |
| FW-010 | Start labeled matching set (≥ 150 question pairs: same / different) | M | AI/QA | S0–S1 | CSV in `apps/api/evals/matching/` |
| FW-011 | Auto-deploy to staging on merge to `main` | M | INFRA | S1 | Merge → staging updated in < 15 min |
| FW-012 | Sentry in extension + API with personal-data scrubbing | S | INFRA | S1 | Test error appears in Sentry with no personal data |

## E1: Accounts & profile (Sprint 1)

| ID | Ticket | Size | Owner | Sprint | Acceptance criteria |
|---|---|---|---|---|---|
| FW-101 | Database schema v1 + Alembic migrations (design §6) | M | API | S1 | Migrations apply/rollback cleanly in CI |
| FW-102 | `/auth/google`, `/auth/refresh` (JWT 15 min + rotating refresh) | M | API | S1 | Tests: valid, expired and tampered tokens |
| FW-103 | Extension sign-in via `chrome.identity` | M | EXT | S1 | User signs in from the side panel; tokens in `chrome.storage.local` |
| FW-104 | `/profile` GET/PUT with schema validation | S | API | S1 | Pydantic schema shared to TS via `shared-schemas` |
| FW-105 | Profile editor UI (identity, education, experience, skills, links) | L | EXT | S1 | Create/edit/save; validation messages |
| FW-106 | Cross-user access test harness | S | API/QA | S1 | Every endpoint test file includes a "user B can't read user A" test |

**Milestone M1:** walking skeleton.

## E2: Resume parsing & knowledge base (Sprint 2)

| ID | Ticket | Size | Owner | Sprint | Acceptance criteria |
|---|---|---|---|---|---|
| FW-201 | `POST /resumes`: upload to S3, SHA-256 dedupe, size/page limits | M | API | S2 | Same file twice → same `resumeId`; > 5 MB rejected |
| FW-202 | Text extraction (PDF + DOCX) with no-text-layer detection | M | API | S2 | Scanned PDF → `parse_status=no_text` |
| FW-203 | LLM resume → structured JSON (schema-validated, versioned prompt) | M | AI | S2 | ≥ 95% field accuracy on 10 sample resumes |
| FW-204 | Parsed-resume review screen (confirm/fix before use) | M | EXT | S2 | User edits are saved to the profile and resume record |
| FW-205 | Build `knowledge_items` from profile + resume (embedded, sensitive-tagged) | M | AI | S2 | Items regenerate when the profile changes |

## E3: Form engine (Sprints 2–3)

| ID | Ticket | Size | Owner | Sprint | Acceptance criteria |
|---|---|---|---|---|---|
| FW-301 | Site adapter interface + adapter registry | S | EXT | S2 | `detect/extractFields/extractJobContext/fill/onSubmit` typed |
| FW-302 | Greenhouse adapter: field extraction | L | EXT | S2 | Matches expected field lists on all Greenhouse snapshots |
| FW-303 | Field classifier (rules + keyword lists → field classes) | M | AI | S2 | ≥ 95% correct class on snapshot fields |
| FW-304 | `POST /fill/plan` (profile + resume sources; tiers; actions) | L | API | S3 | Returns a valid FillPlan for all snapshot fixtures |
| FW-305 | Filler: React-safe setting for text/select/radio/checkbox/file | L | EXT | S3 | Values stick after blur and form validation on Greenhouse + Lever |
| FW-306 | **Never overwrite** (fill empty only; `suggest` for differences; overwrite toggle) | M | EXT/API | S3 | Test: pre-filled fields unchanged unless toggled |
| FW-307 | Fill preview side panel (approve all / per field / edit) | L | EXT | S3 | Nothing is written before approval |
| FW-308 | Field badges in closed Shadow DOM (source + tier) | M | EXT | S3 | Page scripts can't read badge content |
| FW-309 | Lever adapter | L | EXT | S3 | Matches expected field lists on Lever snapshots |
| FW-310 | Fill accuracy test runner over all snapshots (CI) | M | QA | S3 | Reports accuracy per ATS; CI fails below 90% |

**Milestone M2:** fills a Greenhouse form end to end.

## E4: Answer bank & matching (Sprint 4)

| ID | Ticket | Size | Owner | Sprint | Acceptance criteria |
|---|---|---|---|---|---|
| FW-401 | Question normalizer + exact hash lookup | S | AI | S4 | Unit tests on 50 phrasing variants |
| FW-402 | Embeddings + pgvector HNSW search + tier thresholds | M | AI | S4 | Eval: 0 wrong green-tier matches; recall reported |
| FW-403 | "Ask" prompt in side panel → `POST /answers` → fill field | M | EXT | S4 | Answered once → auto-filled on the next fixture with the same question |
| FW-404 | Link new wording as a variant (`/answers/{id}/variants`) on confirm/edit | S | API | S4 | Variant stored with embedding |
| FW-405 | Option mapping (exact → fuzzy → LLM for non-sensitive only) | M | AI | S4 | ≥ 95% correct on the option-mapping test set |
| FW-406 | Sensitive-field rules (`/rules`) + rules UI | M | API/EXT | S4 | EEO/sponsorship follow rules; shown in preview |
| FW-407 | **Sensitive guard** + "no sensitive data in prompt" test | M | AI | S4 | CI test fails if a sensitive value appears in any prompt |
| FW-408 | Answer bank manager (list, search, edit, delete) | M | EXT | S4 | All CRUD from the side panel |
| FW-409 | Encrypt `answers.value_enc` and `application_fields.value_enc` (AES-GCM, KMS key) | M | API | S4 | Plaintext not visible in the database |

**Milestone M3:** learns and never asks twice.

## E5: Grounded generation & resume precedence (Sprint 5)

| ID | Ticket | Size | Owner | Sprint | Acceptance criteria |
|---|---|---|---|---|---|
| FW-501 | Job description extraction per adapter | M | EXT | S5 | Description text captured on all snapshots |
| FW-502 | `POST /generate`: retrieve facts → prompt → JSON + citations → validate | L | AI | S5 | Schema-valid; citations reference real fact IDs |
| FW-503 | Prompt-injection hardening (untrusted block, tests with hostile job descriptions) | M | AI | S5 | 10 injection tests pass |
| FW-504 | Draft review UI with clickable citations | M | EXT | S5 | Clicking a citation shows the source fact |
| FW-505 | Generation eval suite (groundedness, relevance, length) + Langfuse tracing | M | AI/QA | S5 | 0 unsupported claims on a 30-question set |
| FW-506 | Upload watcher (input change + drag-and-drop) → resume upload → session state | M | EXT | S5 | Attached resume detected on all three ATSs |
| FW-507 | Resume precedence in `/fill/plan` (attached → default → profile) + fallback messages | M | API | S5 | Tests for each precedence case + scanned-PDF fallback |
| FW-508 | Ashby adapter | L | EXT | S5 | Matches expected field lists on Ashby snapshots |
| FW-509 | Submission detection + `POST /applications` + application log UI | M | EXT/API | S5 | Log shows company, role, date, resume used |

**Milestone M4:** writes grounded answers.

## E6: Hardening & beta (Sprint 6)

| ID | Ticket | Size | Owner | Sprint | Acceptance criteria |
|---|---|---|---|---|---|
| FW-601 | `GET /me/export` + `DELETE /me` (database + S3) | M | API | S6 | End-to-end test: deleted user has no rows or objects left |
| FW-602 | Rate limits + per-user daily LLM budget + global alert | M | API | S6 | Limits return 429 with a clear message |
| FW-603 | Playwright end-to-end tests with the extension loaded (one flow per ATS) | L | QA | S6 | Run nightly in CI |
| FW-604 | Security checklist pass (threat model §4) | M | All | S6 | All boxes ticked or recorded as accepted risk |
| FW-605 | Production environment + manual-approval deploy | M | INFRA | S6 | Prod deploy requires approval |
| FW-606 | Runbook: "site adapter broken" + "LLM provider down" | S | PM/INFRA | S6 | In `docs/operations/runbooks/` |
| FW-607 | Dogfooding: 20+ real applications; log every issue | M | All | S6 | Issues triaged into the backlog |
| FW-608 | Beta onboarding guide + feedback form | S | PM | S6 | Shared with beta users |

**Milestone M5:** MVP closed beta.

---

**Totals:** 59 tickets · 7 epics (E0–E6) · 7 sprints (S0–S6).
