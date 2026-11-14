# Fillwell Docs

**Fillwell** is a Chrome extension that autofills job applications, learns each new question once, and writes grounded answers with citations. It never submits, and never overwrites.

!!! note "New to the project?"
    Read the docs in the order below. Anything unclear → open an issue or comment on the PR that last changed the page.

## Read in this order

| # | Doc | What it answers | Who must read |
|---|---|---|---|
| 1 | [PRD](product/prd.md) | What are we building, for whom, and what's in V1 / V2 / V3? | Everyone |
| 2 | [Feature list](product/feature-list.md) | Full feature catalog with IDs (C\*, S\*, L\*) and versions | Everyone |
| 3 | [Competitive landscape](research/competitive-landscape.md) | Who else does this; where we're different | Everyone |
| 4 | [Technical design](design/technical-design.md) | How it works: architecture, data model, API, flows | Engineers |
| 5 | [ADRs](adr/index.md) | Why we chose each technology | Engineers |
| 6 | [Threat model](design/threat-model.md) | What could go wrong security-wise | Engineers |
| 7 | [Privacy & data handling](design/privacy-data-handling.md) | What data we keep, where, and for how long | Everyone |
| 8 | [Roadmap](planning/roadmap.md) | Sprint plan, milestones, release dates | Everyone |
| 9 | [MVP backlog](planning/mvp-backlog.md) | Every MVP ticket with acceptance criteria | Everyone |
| 10 | [Test plan](planning/test-plan.md) | How we know it works | Engineers + QA |
| 11 | [Contributing](engineering/contributing.md) | How we work: branches, PRs, definition of done | Engineers |

## Current status

- **Phase:** Sprint 0 (design review + setup), Nov 16 – Nov 29, 2026
- **Next milestone:** M0 Ready to build → M1 Walking skeleton (Dec 13)
- **MVP target:** closed beta ~Feb 22, 2027

## Sprint 0 checklist

- [ ] Assign people to roles ([Contributing §1](engineering/contributing.md))
- [ ] Team reads docs 1–8; comments added
- [ ] Design review meeting → docs approved (FW-001)
- [ ] Create the GitHub Project board; import [MVP backlog](planning/mvp-backlog.md) tickets as issues
- [ ] Start the spikes (FW-007, FW-008) and fixture collection (FW-009)

## Editing these docs

Every page has an **Edit this page** button (top right). Changes go through a pull request and show up in each page's footer and in the [docs changelog](changelog.md). See [How to edit](how-to-edit.md).
