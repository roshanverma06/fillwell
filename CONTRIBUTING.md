# Fillwell: Team Workflow & Contributing Guide

*Published copy: `docs/engineering/contributing.md` on the docs site.*

## 1. Roles (assign people in Sprint 0)

| Role | Owns |
|---|---|
| **Product lead (PM)** | PRD, backlog priority, beta users, demo |
| **Tech lead** | Design doc, ADRs, code review standards, release approval |
| **Extension engineer** | WXT app, site adapters, filler, side panel UI |
| **Backend engineer** | FastAPI, database, auth, export/delete |
| **AI engineer** | Classifier, matching, generation, prompts, evals |
| **DevOps/infra** | CI/CD, Terraform, environments, monitoring |
| **QA owner** | Fixtures, test plan, end-to-end tests, bug triage |

One person can hold more than one role. Every role should have a backup.

## 2. Getting started
```bash
git clone git@github.com:<org>/fillwell.git && cd fillwell
cp .env.example .env            # fill in local values; never commit .env
make setup                      # pnpm install + uv sync + pre-commit hooks
make db-up                      # local Postgres + pgvector via Docker
make dev                        # API on :8000, extension dev build with hot reload
# Chrome → chrome://extensions → Developer mode → Load unpacked → apps/extension/.output/chrome-mv3
```

## 3. Work tracking
- All work is a **GitHub Issue** in the Fillwell Project board: `Backlog → Ready → In progress → In review → Done`.
- **Definition of Ready** (before a ticket enters a sprint): clear acceptance criteria, size estimated, dependencies known, owner agreed.
- **Definition of Done:**
    - [ ] Acceptance criteria met and demoed on staging
    - [ ] Tests added/updated; CI green
    - [ ] No personal data in logs; security implications considered
    - [ ] Docs/ADR updated if behavior or architecture changed
    - [ ] Reviewed and approved by at least 1 teammate

## 4. Branches & commits
- **Trunk-based:** `main` is always deployable. Short-lived branches, merged within ~3 days.
- Branch name: `<type>/FW-<id>-short-description`, e.g. `feat/FW-305-react-safe-filler`.
- **Conventional Commits:** `feat:`, `fix:`, `test:`, `docs:`, `refactor:`, `chore:`, plus the ticket id:
  `feat(extension): React-safe select filling (FW-305)`
- Unfinished features go behind a **feature flag** instead of a long-lived branch.

## 5. Pull requests
- Keep PRs small (aim for < 400 changed lines).
- PR template: **What / Why / How tested / Screenshots / Risks / Ticket**.
- 1 approval required; the tech lead must also approve changes to auth, encryption, the sensitive guard, prompts or ADR-008 guardrails (enforced by `.github/CODEOWNERS`).
- Squash-merge into `main` → auto-deploy to staging.
- **Reviewer checklist:** correctness · tests · readability · security (user_id filtering, input validation, no personal data in logs) · ADR-008 guardrails respected.

## 6. Environments

| Env | URL | Deploys | Data |
|---|---|---|---|
| Local | localhost | `make dev` | Synthetic only |
| Staging | `api-staging.<domain>` | Every merge to `main` | Synthetic + team test accounts |
| Production | `api.<domain>` | Manual approval of a tagged release | Real users |

## 7. Releases
- Versioning: **SemVer** (`v1.0.0` = MVP). Extension and API share a version number.
- `CHANGELOG.md` updated in every release PR.
- Release checklist: release gate in the test plan §7 → tag → prod deploy approval → extension zip → beta channel.

## 8. Documentation
- Docs live in `docs/` and publish automatically to the docs site on merge. See [How to edit](docs/how-to-edit.md).
- Code PRs that change behavior update the relevant doc **in the same PR**.

## 9. Communication
- **Async standup** in the team channel daily: *Yesterday / Today / Blockers*.
- Decisions made in chat get written into an ADR or the ticket, not lost in chat.
- When something breaks in staging/prod: post in `#fillwell-incidents`, fix, then write a short postmortem (no blame).

## 10. Rules that never bend (ADR-008)
1. Never submit an application.
2. Never overwrite by default.
3. Sensitive fields never reach an LLM.
4. Every filled value has a source.
5. Uncertain means ask.
