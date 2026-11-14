# ADR-004: LLM behind a provider interface; in-service open-source embeddings

**Status:** Proposed (confirm after Sprint 0 spike FW-008) · **Date:** 2026-11-14

## Context
We need (a) **generation**: drafting written answers and turning resume text into structured JSON, and (b) **embeddings**: turning question text into vectors to match reworded questions. Quality, cost, privacy and vendor lock-in all matter.

## Decision
1. **Generation:** call LLMs through a small `LLMProvider` interface (`complete(prompt, schema) -> json`). The default provider is **Claude via the Anthropic API**; the provider is switchable by config.
2. **Embeddings:** run a small **open-source sentence-embedding model** (384 dimensions, e.g. the `bge-small-en` family via `sentence-transformers`) **inside the API service**.
3. Prompts are **versioned files** in the repo (`apps/api/prompts/`), and every generation records its prompt version.

## Why
- Running embeddings ourselves means **question text never leaves our servers for matching**, there's no per-call cost, and latency is low.
- A provider interface keeps us vendor-neutral and lets the eval suite compare models.

## Alternatives considered
- **Hosted embedding API:** possibly better quality, but per-call cost, another third party, and question text sent out for every match.
- **One provider hard-coded everywhere:** faster to write, painful to change.

## Consequences
- **Benefit:** Privacy-friendly matching; the cost per application is mainly generation.
- **Trade-off:** The model adds memory (~150–500 MB) to the API container. Decide in the spike whether it stays in-process or becomes a separate service.
- **Revisit:** if eval recall on reworded questions is below target.
