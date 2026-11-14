# ADR-002: FastAPI (Python) backend

**Status:** Accepted · **Date:** 2026-11-14

## Context
We need an API for profile storage, matching, resume parsing and LLM generation. Most AI/ML tooling (embedding models, eval libraries, LLM SDKs) is strongest in Python, and the team's strongest language is Python.

## Decision
Use **FastAPI** with **Pydantic v2** (validation), **SQLAlchemy 2 + Alembic** (database access and migrations), and **uv** (dependency management). Tests with **pytest**.

## Alternatives considered
- **Node.js (Express/NestJS):** one language across the stack, but weaker for embeddings and evals.
- **Django:** more built in (admin, ORM), but heavier and less natural for an async, API-only service.
- **Serverless functions only (Lambda):** cheap at low traffic, but cold starts hurt latency, and the embedding model would have to load on every cold start.

## Consequences
- **Benefit:** Automatic OpenAPI docs, async support, the strongest AI ecosystem.
- **Trade-off:** Two languages in the monorepo (TS + Python): types are shared via JSON Schema generated from Pydantic models.
