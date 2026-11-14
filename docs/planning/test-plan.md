# Fillwell: Test Plan (MVP)

| | |
|---|---|
| **Status** | Draft v0.1 |
| **Owner** | QA owner (assign) |
| **Last updated** | November 14, 2026 |

## 1. What we test and why

Fillwell can fail in three different ways, and each needs its own kind of testing:

1. **Normal software bugs** → unit, integration and end-to-end tests.
2. **Job sites changing their pages** → saved-form snapshot tests.
3. **AI being wrong** (bad matches, invented claims) → **eval suites** with labeled data and fixed pass/fail thresholds.

## 2. Test levels

| Level | Tool | Scope | Runs |
|---|---|---|---|
| **Unit (API)** | pytest | Normalizer, classifier rules, precedence logic, sensitive guard, encryption, schemas | Every PR |
| **Unit (extension)** | Vitest | Adapters' parsing helpers, filler event logic, message validation | Every PR |
| **Integration (API)** | pytest + test Postgres (Docker) | Endpoints with a real database + pgvector; LLM mocked | Every PR |
| **Cross-user security** | pytest | Every endpoint: user B can't read or change user A's data | Every PR |
| **Snapshot / adapter** | Vitest + jsdom/happy-dom over `fixtures/forms/` | Field extraction + classification accuracy per ATS | Every PR (extension changes) |
| **End-to-end** | Playwright with the extension loaded, against local fixture pages + staging API | Full flow: sign in → fill → ask → generate → log | Nightly + before release |
| **Matching eval** | Python script | Labeled question pairs → precision, recall, wrong-green rate per threshold | When matching code/model/thresholds change; nightly |
| **Generation eval** | Python script + LLM-as-judge + manual spot checks | Groundedness, citation validity, relevance, length | When prompts/models change; nightly (small set) |
| **Prompt-injection suite** | pytest | 10+ hostile job descriptions | Every PR touching prompts |
| **Manual QA** | Checklist | Real live postings on each ATS | Before each sprint demo |
| **Dogfooding** | Real applications | Everything | Sprint 6 onward |

## 3. Test data

| Dataset | Location | Contents | Rules |
|---|---|---|---|
| Form snapshots | `fixtures/forms/<ats>/<company>.html` + `.expected.json` | 30+ real form pages with expected field lists and classes | Remove any personal data; refresh quarterly |
| Matching set | `apps/api/evals/matching/pairs.csv` | ≥ 150 labeled pairs `question_a, question_b, same(bool), class` | Include tricky near-misses ("Do you require sponsorship **now**" vs "**in the future**") |
| Option-mapping set | `apps/api/evals/options/cases.json` | Stored answer + option lists + expected choice | Cover yes/no phrasings, ranges, "Decline to answer" |
| Generation set | `apps/api/evals/generation/questions.json` | 30 descriptive questions + 3 synthetic profiles + 5 job descriptions | **Synthetic profiles only**, no real user data |
| Sample resumes | `fixtures/resumes/` | 10 synthetic resumes (PDF + DOCX), including one scanned | Synthetic only |

## 4. MVP pass/fail targets

| Area | Metric | Target |
|---|---|---|
| Adapters | Field extraction accuracy per ATS | ≥ 95% |
| Classifier | Correct field class | ≥ 95% |
| Overall fill | Correct value on snapshot fields | ≥ 90% |
| Matching | Wrong matches in the green tier | **0** |
| Matching | Recall of true matches (green + yellow) | ≥ 85% |
| Option mapping | Correct choice | ≥ 95% |
| Generation | Unsupported claims | **0** |
| Generation | Invalid citations | **0** |
| Privacy | Sensitive values found in prompts | **0** |
| Resume parsing | Field accuracy on sample resumes | ≥ 95% |
| Coverage | API line coverage | ≥ 80% (business logic) |

## 5. Manual QA checklist (per ATS, before each demo)
- [ ] Fresh install → sign in → upload resume → confirm parsed data
- [ ] Fill a live posting: preview shown, nothing written before approval
- [ ] Pre-typed field stays unchanged; difference shown as a suggestion
- [ ] Attach a different resume → resume-type fields use it; source tags show its filename
- [ ] New question → asked → second posting with reworded question fills at the green or yellow tier
- [ ] EEO + sponsorship follow rules; shown in preview
- [ ] "Why us?" draft has citations; clicking shows the facts
- [ ] Fillwell never clicks Submit
- [ ] After submitting manually → appears in the application log
- [ ] Side panel usable with keyboard only

## 6. Bug severity

| Severity | Definition | Example | Response |
|---|---|---|---|
| **S1 – Critical** | Wrong data could be submitted, data leak, or crash on all sites | Sponsorship filled wrong in the green tier; user B sees user A's data | Stop the sprint; fix immediately |
| **S2 – High** | Main flow broken on one ATS | Lever selects don't stick | Fix this sprint |
| **S3 – Medium** | Workaround exists | Badge misplaced | Schedule |
| **S4 – Low** | Cosmetic | Typo | Backlog |

## 7. Release gate (MVP)
All of §4's targets met, no open S1/S2 bugs, security checklist complete, end-to-end suite green on three consecutive nightly runs.
