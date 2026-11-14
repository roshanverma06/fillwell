# Fillwell: Product Requirements Document (PRD)

| | |
|---|---|
| **Status** | Draft v0.1, ready for team review |
| **Owner** | Product lead (assign) |
| **Last updated** | November 14, 2026 |
| **Related** | `product/feature-list.md`, `research/competitive-landscape.md`, `design/technical-design.md`, `planning/roadmap.md` |

---

## 1. Problem

Job seekers fill out the same application forms hundreds of times. Existing autofill tools (Simplify, Jobright, Huntr and others) handle basic fields, but:

1. **They overwrite your work.** Clicking autofill replaces fields with data from a generic stored resume, even when the user has attached a resume tailored to this job.
2. **They get custom questions wrong**, and AI-written answers can include claims the user never made.
3. **They're black boxes.** Users can't see where a value came from or why a saved answer was reused.
4. **Answers go stale.** "Are you a student?", start dates and years of experience change, but saved answers don't.
5. **The best AI features cost $20–40 a month.**

## 2. Vision

> **Fillwell is autofill you can trust. Answer once; Fillwell remembers, and explains every fill.**

## 3. Target users

| Persona | Description | Key need |
|---|---|---|
| **High-volume applicant** (primary) | Recent grad or early-career engineer applying to 10–30 roles a week across several role types | Speed without wrong answers |
| **Careful tailorer** | Tailors a resume per job and needs autofill to respect it | The tailored resume is used, nothing overwritten |
| **Privacy-conscious applicant** | Worried about where sensitive answers (visa, EEO) go | Control and transparency |

## 4. Goals and non-goals

### Goals (MVP)
- G1: Fill a standard Greenhouse, Lever or Ashby application in **under 3 minutes**, with the user reviewing everything.
- G2: **Never ask the same question twice** once it's answered, including when it's reworded.
- G3: **Never overwrite** a value the user or the site already entered, unless the user explicitly asks.
- G4: **The resume attached to an application wins** over the stored default.
- G5: AI-written answers use **only facts the user has stored**, with citations.
- G6: Sensitive answers (EEO, sponsorship, disability, veteran) are **never sent to an LLM**.

### Non-goals
- Auto-submitting or auto-applying (the user always clicks Submit)
- LinkedIn Easy Apply automation (against LinkedIn's terms; account risk)
- Job discovery or matching
- Resume building or editing
- Getting around CAPTCHAs or bot protection
- Mobile apps

## 5. Release modules

Features are shipped in versions. Feature IDs (C\*, S\*, L\*) refer to `product/feature-list.md`.

### V1: MVP, "Fill & Remember"
**Goal:** a trustworthy autofill that learns, for the three most predictable ATSs.
**Sites:** Greenhouse, Lever, Ashby.

| ID | Feature | MVP scope |
|---|---|---|
| C1 | Profile & knowledge base | One persona, full profile editor |
| C2 | Resume upload & parsing | PDF + DOCX → structured profile (user confirms) |
| C3 | Form detection & autofill | Text, textarea, select, radio, checkbox, file upload |
| C4 | Ask once, never again | Exact match + meaning match (embeddings) |
| C5 | Option matching | Fuzzy match, with LLM fallback |
| C6 + S1 | AI written answers **with citations** | Grounded on stored facts + job description |
| C7 | Review before submit | Always |
| C8 | Basic application log | Company, role, URL, date, resume used |
| S3 | Confidence levels | Green / yellow / red thresholds |
| S4 | Fill preview | Side panel table before writing to the page |
| S5 | Source tags | Per field |
| S6 | Uploaded resume wins | Precedence rules |
| S7 | Never overwrite | Fill empty fields only by default |
| S9 | Per-application resume data | Cached by file fingerprint |
| S14 (basic) | Answer bank manager | List, search, edit, delete |
| S15 | Sensitive-field rules | Never sent to LLM; encrypted |
| — | Accounts & data rights | Google sign-in, export, delete all |

### V2: "Trust & Memory"
**Adds:** Workday support.

| ID | Feature |
|---|---|
| S2 | Honesty guard (checks claims against stored facts) |
| S8 | Re-sync when a resume is uploaded after filling |
| S10 | Expiring answers |
| S11 | Consistency checker |
| S12 | Knock-out question detector |
| S13 | Personas (DE / AE / SWE / DA) |
| S14 (full) | Answer bank: merge duplicates, version history |
| S16 | Application snapshot vault |
| L1 | Length-aware writing |

### V3: "Scale & Launch"
**Adds:** iCIMS, SmartRecruiters. **Public Chrome Web Store launch.**

| ID | Feature |
|---|---|
| S17 | Duplicate-application guard |
| S18 | Resume pipeline connection |
| L2 | Teach mode for unknown fields |
| L3 | Save & resume long forms |
| L4 | Workday account tracker |
| L7 | Personal stats |
| — | Remote site settings (fix broken selectors without a store release), fill-rate monitoring |

### V4+: "Intelligence"
L5 learning from edits · L6 interview prep from snapshots · L8 results analytics · L9 follow-up drafts · L10 voice answers · L11 local-only mode · L12 shared question dictionary · L13 open-source release.

## 6. MVP user stories

| ID | As a… | I want… | So that… |
|---|---|---|---|
| US-01 | new user | to sign in with Google and upload my resume | my profile is built in under 5 minutes |
| US-02 | user | to review and correct what was parsed from my resume | the data Fillwell uses is accurate |
| US-03 | user | to click one button on a Greenhouse, Lever or Ashby form | known fields are filled for me |
| US-04 | user | to see a preview of every value and its source before anything is written | I stay in control |
| US-05 | user | Fillwell to leave fields I or the site already filled untouched | my work isn't overwritten |
| US-06 | careful tailorer | Fillwell to use the resume I attached to this application | my tailored details are used, not the default |
| US-07 | user | to be asked a new question only once | I never retype the same answer |
| US-08 | user | reworded versions of a question to be recognized | "Do you need sponsorship?" and "Will you require visa sponsorship?" match |
| US-09 | user | low-confidence matches to be highlighted, not silently filled | wrong answers don't slip through |
| US-10 | user | a draft for "Why do you want to work here?" based on my real experience and the job | I can edit instead of starting from a blank box |
| US-11 | user | to see which of my facts each AI answer used | I can trust and verify it |
| US-12 | privacy-conscious user | EEO and sponsorship answers to follow my rules and never go to an AI | my sensitive data stays private |
| US-13 | user | to view, edit and delete my saved answers | my answer bank stays correct |
| US-14 | user | a log of where I applied and which resume I used | I can keep track |
| US-15 | user | to export or permanently delete all my data | I own my data |

## 7. Functional requirements (MVP)

### 7.1 Filling rules
- **FR-1 Precedence:** resume-type fields use (1) the resume attached to this application → (2) the default resume → (3) the base profile. Personal and legal fields always come from the profile or answer bank.
- **FR-2 Non-destructive:** fill empty fields only. If a field has a value that differs from Fillwell's, show a suggestion and don't change it. "Overwrite all" is a per-application toggle, off by default.
- **FR-3 Confidence:** semantic match ≥ 0.90 → green (fill); 0.80–0.90 → yellow (fill and highlight); < 0.80 → red (ask the user). These are starting values to be tuned on the labeled test set (see test plan).
- **FR-4 Source tag:** every filled field records its source (`profile`, `resume:<filename>`, `answer_bank:<id>`, `ai_generated:<id>`, `rule:<id>`).
- **FR-5 Never submit:** Fillwell never clicks Submit, Next-and-submit, or anything equivalent.
- **FR-6 Sensitive fields:** fields classified as EEO, disability, veteran, sponsorship, work authorization or salary follow user rules. Their values are never included in LLM prompts.
- **FR-7 Grounding:** generated answers may only use facts retrieved from the user's knowledge base plus the job description. Each answer returns citations. If there isn't enough information, it says so instead of inventing.

### 7.2 Supported field types (MVP)
Text, email, phone, URL, number, date, textarea, native select, custom React selects on supported sites, radio, checkbox, file upload (resume / cover letter).

## 8. Success metrics

| Metric | MVP target | How it's measured |
|---|---|---|
| Field fill accuracy | ≥ 90% correct on the saved test forms | Automated snapshot tests |
| Wrong auto-fills from the answer bank | 0 in the labeled test set; < 1% in beta | Eval + beta telemetry (fields the user corrected) |
| Time per Greenhouse application | < 3 minutes median | Beta timing |
| Repeat questions asked | 0 for exact matches; < 5% for reworded ones | Eval set |
| Unsupported claims in generated answers | 0 in eval set | Groundedness eval |
| Generated answers kept with light or no edits | ≥ 60% | Edit-distance telemetry |
| Sensitive values in LLM prompts | 0 | Automated test + prompt-log audit |

## 9. Constraints and assumptions
- Chrome (Manifest V3) only for MVP; Edge works for free as a Chromium browser.
- English-language forms only.
- Beta is closed (5–20 users); public launch is in V3.
- The team works part-time; see `planning/roadmap.md` for capacity assumptions.

## 10. Risks

| Risk | Impact | Mitigation |
|---|---|---|
| ATS markup changes break filling | High | Adapter per site, snapshot tests, remote site settings in V3 |
| Similarity threshold too loose → wrong answers | High | Labeled eval set, yellow review tier, conservative default |
| LLM invents claims | High | Grounding, citations, honesty guard in V2 |
| Prompt injection through job description text | Medium | See `design/threat-model.md` |
| Chrome Web Store rejection (permissions, privacy) | Medium | Minimal permissions, privacy policy before V3 |
| Scope creep | Medium | Feature IDs locked per version; changes go through the PRD |

## 11. Open questions
1. Team size and roles: who owns extension, backend, AI and QA? (Affects the roadmap.)
2. Hosted backend only, or also a no-backend local mode? (MVP: hosted.)
3. Which LLM and embedding providers in production? (ADR-004 sets the starting choice.)
4. Do we keep the generated-answer history for edit learning in V4? (Affects the retention policy.)
