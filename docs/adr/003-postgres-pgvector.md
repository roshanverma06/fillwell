# ADR-003: PostgreSQL + pgvector as the only database

**Status:** Accepted · **Date:** 2026-11-14

## Context
We store relational data (users, answers, applications) and vectors (embeddings of question wordings and facts) for meaning-based search. Each user will have hundreds to a few thousand vectors.

## Decision
Use **PostgreSQL 16 with the pgvector extension** for both. Use HNSW indexes on embedding columns, with every query filtered by `user_id`.

## Alternatives considered
- **Dedicated vector DB (Pinecone, Weaviate, Qdrant):** built for billions of vectors, but adds a second system to secure, back up, keep in sync and pay for. Our scale doesn't need it.
- **SQLite + local vectors:** good for a local-only mode later (L11), not for a hosted multi-user service.

## Consequences
- **Benefit:** One database: transactions keep answers and their vectors in sync; one backup; one access-control model.
- **Benefit:** Supported on AWS RDS.
- **Trade-off:** If we ever reach millions of users, we may need a specialized vector store. Not a concern for MVP.
