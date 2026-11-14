# ADR-007: Monorepo layout

**Status:** Accepted · **Date:** 2026-11-14

## Decision
One GitHub repository:

```
fillwell/
├── apps/
│   ├── extension/          # WXT + TS + React
│   └── api/                # FastAPI
│       ├── app/            # routers, services, repositories, models
│       ├── prompts/        # versioned prompt templates
│       ├── evals/          # matching + generation eval suites
│       └── tests/
├── packages/
│   └── shared-schemas/     # JSON Schema generated from Pydantic → TS types
├── fixtures/
│   └── forms/              # saved ATS form HTML snapshots for tests
├── infra/                  # Terraform
├── docs/                   # this documentation site (MkDocs)
├── mkdocs.yml
└── .github/workflows/      # CI/CD
```

Tooling: **pnpm** workspaces for JS, **uv** for Python, a root `Makefile` with `make dev`, `make test`, `make lint`.

## Why
A change to the fill-plan schema touches the API and the extension together. One repo means one PR, one review and one CI run, so they can't drift apart.

## Consequences
- **Benefit:** Atomic changes; shared fixtures and schemas; docs change in the same PR as the code they describe.
- **Trade-off:** CI must run only the jobs affected by a change (path filters) to stay fast.
