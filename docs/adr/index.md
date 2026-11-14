# Architecture Decision Records (ADRs)

> **What's an ADR?** A short record of one important technical decision: the situation, what we decided, why, and what we gave up. When someone asks "why did we use X?" six months later, the answer is here. New decisions get the next number; old ADRs are never edited. If a decision changes, a new ADR replaces the old one.

**Template:** Status · Context · Decision · Alternatives considered · Consequences

| # | Title | Status |
|---|---|---|
| [001](001-extension-framework.md) | Chrome MV3 extension built with WXT + TypeScript + React | Accepted |
| [002](002-backend-fastapi.md) | FastAPI (Python) backend | Accepted |
| [003](003-postgres-pgvector.md) | PostgreSQL + pgvector as the only database | Accepted |
| [004](004-llm-and-embeddings.md) | LLM behind a provider interface; in-service open-source embeddings | Proposed |
| [005](005-auth.md) | Google sign-in via chrome.identity + backend JWT | Accepted |
| [006](006-hosting-aws.md) | AWS hosting: App Runner + RDS + S3 | Proposed |
| [007](007-monorepo.md) | Monorepo layout | Accepted |
| [008](008-product-guardrails.md) | Product guardrails: never submit, never overwrite, sensitive fields never reach the LLM | Accepted |

## Adding a new ADR

1. Copy the template below to `docs/adr/NNN-short-title.md` (next number).
2. Add it to this table and to `nav` in `mkdocs.yml`.
3. Open a PR; the tech lead approves (enforced by CODEOWNERS).

```markdown
# ADR-NNN: Title

**Status:** Proposed · **Date:** YYYY-MM-DD

## Context
## Decision
## Alternatives considered
## Consequences
```
