# ADR-006: AWS hosting: App Runner + RDS + S3

**Status:** Proposed (confirm budget) · **Date:** 2026-11-14

## Context
We need a staging and a production environment for one containerized API, a managed Postgres with pgvector, private file storage, and secrets management. The team has AWS certification experience. The budget is small.

## Decision
- **API:** a Docker container on **AWS App Runner** (managed, autoscaling, HTTPS built in).
- **Database:** **RDS PostgreSQL 16** with pgvector (small instance for staging and prod).
- **Files:** a private **S3** bucket, encrypted with KMS.
- **Secrets:** **AWS Secrets Manager**.
- **Infrastructure as code:** **Terraform** in `infra/`.
- **Environments:** `staging` (auto-deploy on merge to `main`) and `prod` (manual approval).

## Alternatives considered
- **ECS Fargate:** more control, more setup; move here if App Runner becomes limiting.
- **Render / Railway / Fly.io:** faster to start and cheaper at tiny scale, but less relevant for AWS-focused resumes and interviews.
- **Lambda:** cold starts plus the embedding model make it a poor fit (see ADR-002).

## Consequences
- **Benefit:** Production-grade setup that's good resume material; separate staging and prod.
- **Trade-off:** RDS has a monthly cost even when idle; set AWS budget alerts on day one.
