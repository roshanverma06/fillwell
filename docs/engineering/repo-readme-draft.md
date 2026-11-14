# Repo README (draft)

*This is the draft for the public `README.md` at the repo root. The live copy is in the repo; keep them in sync until the code exists.*

---

# Fillwell

> **Autofill you can trust.** Answer once; Fillwell remembers, and explains every fill.

Fillwell is a Chrome extension that fills job applications from your profile and resume, learns every new question you answer so it never asks twice, and drafts written answers grounded only in facts you've provided, with citations. It never submits for you, and it never overwrites what you've already entered.

`[ demo GIF goes here ]`

## Why Fillwell?
| Problem with existing tools | Fillwell |
|---|---|
| Autofill overwrites your tailored resume details | **The resume you attach wins**, and filled fields are never overwritten |
| AI answers include things you never said | **Grounded answers with citations** |
| No idea why a value was filled | **Source tag + confidence** on every field |
| Sensitive answers sent who knows where | **EEO and sponsorship answers never reach an AI** |

## Features (V1)
- One-click fill for **Greenhouse, Lever and Ashby**, with a preview before anything is written
- **Ask once, never again**: matches reworded questions by meaning
- Green / yellow / red confidence tiers
- Grounded answers to "Why this company?"-style questions
- Resume upload & parsing; per-application resume precedence
- Answer bank manager; sensitive-field rules; application log
- Full data export and delete

## Architecture
Chrome MV3 extension (WXT · TypeScript · React) → FastAPI (Python) → PostgreSQL + pgvector · S3 · LLM API.

## Quick start (development)
```bash
cp .env.example .env
make setup && make db-up && make dev
```
Load `apps/extension/.output/chrome-mv3` as an unpacked extension in Chrome.

## Roadmap
**V1 (MVP):** Fill & Remember → **V2:** Trust & Memory (+Workday) → **V3:** Scale & public launch → **V4:** Intelligence

## License
TBD (decision before V3, see feature L13).
