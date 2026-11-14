# ADR-008: Product guardrails: never submit, never overwrite, sensitive fields never reach the LLM

**Status:** Accepted · **Date:** 2026-11-14

## Context
Competitors overwrite user-entered data, get custom questions wrong, and some auto-submit low-quality applications. A wrong sponsorship or EEO answer submitted under a user's name causes real harm. These guardrails define the product, so we record them as architecture decisions that code reviews must enforce.

## Decision
1. **Never submit.** No code path clicks Submit or a final "Next" that submits.
2. **Never overwrite by default.** Fill empty fields only; differences become suggestions; overwriting needs an explicit per-application toggle.
3. **Sensitive fields never reach an LLM.** The sensitive guard runs before every prompt is built and is covered by automated tests.
4. **Every filled value has a recorded source**, shown to the user.
5. **Uncertain means ask.** Below the confidence threshold, ask the user; never guess.

## Consequences
- **Benefit:** Trust is the product's main difference from competitors.
- **Trade-off:** Slightly slower than "fill everything" tools. That's the intended trade-off.
- Any PR that weakens these rules needs a new ADR and sign-off from the tech lead.
