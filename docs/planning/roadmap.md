# Fillwell: Roadmap & Release Plan

| | |
|---|---|
| **Status** | Draft v0.1 |
| **Owner** | Product lead + tech lead |
| **Last updated** | November 14, 2026 |

## Capacity assumptions
- **Team:** 3–4 people, part-time (~10–15 hours/week each). If the team is smaller or larger, re-plan at Sprint 0.
- **Cadence:** 2-week sprints. Planning on the first Monday, demo + retro on the last Friday.
- **Buffer:** about 20% of every sprint is reserved for bugs and unknowns.
- Dates are **targets**, reviewed at every sprint retro.

## Release overview

| Release | Theme | Sites | Target | Audience |
|---|---|---|---|---|
| **V1 / MVP** | Fill & Remember | Greenhouse, Lever, Ashby | **Closed beta ~Feb 22, 2027** | Team + 5–20 beta users |
| **V2** | Trust & Memory | + Workday | ~Apr 2027 | Expanded beta |
| **V3** | Scale & Launch | + iCIMS, SmartRecruiters | ~Jun 2027 | **Public Chrome Web Store** |
| **V4+** | Intelligence | — | H2 2027 | Public |

Feature contents per release: see [PRD §5](../product/prd.md#5-release-modules).

## MVP sprint plan

| Sprint | Dates | Focus | Milestone |
|---|---|---|---|
| **S0** | Nov 16 – Nov 29 | Design review sign-off; repo, CI and environments; technical spikes (auth, embeddings, React-safe fill); collect 30+ form snapshots; start the labeled matching set | **M0: Ready to build** |
| **S1** | Nov 30 – Dec 13 | Auth end to end; database schema + migrations; profile API + editor; Sentry; deploy to staging | **M1: Walking skeleton**: a signed-in side panel talks to the staging API |
| **S2** | Dec 14 – Dec 27 *(holiday, ~60% capacity)* | Resume upload + parsing; knowledge items; Greenhouse adapter (field extraction); field classifier; snapshot tests | — |
| **S3** | Dec 28 – Jan 10 | `/fill/plan`; React-safe filler; preview panel; source tags; never-overwrite; Lever adapter | **M2: Fills a Greenhouse form end to end** |
| **S4** | Jan 11 – Jan 24 | Answer bank: ask once; embeddings + matching tiers; option mapping; sensitive rules; answer bank manager; threshold tuning on the eval set | **M3: Learns and never asks twice** |
| **S5** | Jan 25 – Feb 7 | Grounded generation + citations; job description extraction; upload watcher + resume precedence; Ashby adapter; application log | **M4: Writes grounded answers** |
| **S6** | Feb 8 – Feb 21 | Export/delete; rate limits + budgets; security checklist; end-to-end tests; bug bash; dogfooding on real applications; beta onboarding | **M5: MVP closed beta** |

### MVP exit criteria (must all be true to start the beta)
- [ ] All V1 features in the PRD §5 implemented and demoed
- [ ] Fill accuracy ≥ 90% on the saved test forms (Greenhouse, Lever, Ashby)
- [ ] 0 wrong green-tier fills in the labeled matching eval set
- [ ] 0 unsupported claims in the generation eval set
- [ ] Security checklist in the threat model §4 complete
- [ ] Team has used it on at least 20 real applications
- [ ] Runbook for "site adapter broken" written

## V2 / V3 outline (planned in detail after the MVP retro)

**V2 (≈4 sprints, Feb 22 – Apr 18):** Workday adapter (multi-page, custom dropdowns); honesty guard; personas; expiring answers; consistency checker; knock-out detector; full answer bank (merge, versions); snapshot vault; upload-after-fill re-sync; length-aware writing; Postgres row-level security.

**V3 (≈4 sprints, Apr 19 – Jun 13):** iCIMS + SmartRecruiters adapters; remote site settings + fill-rate monitoring; duplicate-application guard; resume pipeline connection; teach mode; save & resume; Workday account tracker; personal stats; public privacy policy; Chrome Web Store listing; staged rollout; pen test.

## Ceremonies

| Meeting | When | Length | Purpose |
|---|---|---|---|
| Sprint planning | Sprint day 1 | 60 min | Pick tickets from the backlog, confirm owners and estimates |
| Async standup | Daily (written, in team chat) | 5 min | Yesterday / today / blockers |
| Mid-sprint sync | Day 6 | 30 min | Unblock; scope check |
| Demo | Sprint last day | 30 min | Show working software on staging |
| Retro | After demo | 30 min | What went well / what didn't / one change to try |
| Design review | As needed | 45 min | Review RFCs/ADRs before big work starts |
