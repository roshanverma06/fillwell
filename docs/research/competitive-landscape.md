# Fillwell: Competitive Landscape

*Phase 0 research, November 13, 2026. Prices change often and many come from third-party reviews; they're marked "reported" where the vendor doesn't publish them.*

## TL;DR

- The market is **crowded and mature**. Simplify alone claims 200M+ applications submitted, and its core autofill is **free forever**.
- Fillwell's headline features are **already table stakes**:
    - Autofill from one profile
    - Saving answers to new questions and reusing them later (Simplify and FrogHire both do this)
    - AI answers to custom questions (usually paid)
- **Where the gaps are:**
    - Custom and descriptive questions are often answered badly.
    - AI features sit behind $20–40/month paywalls.
    - Users have little control or visibility over what's stored and why an answer was chosen.
    - Auto-apply bots have poor quality and a bad reputation.
- **Conclusion:** Fillwell won't win on features against funded companies. As a **portfolio project** it's still strong. The value is in *how well it's engineered* and *how clearly we can explain the hard parts*, not in being first.

## The three categories

### 1. Autofill copilots (fill the form, human clicks Submit)

This is Fillwell's category.

| Product | What it does | Free tier | Paid (reported) | Notes |
|---|---|---|---|---|
| **Simplify Copilot** | One profile, autofill on 100+ portals (Workday, Greenhouse, iCIMS, Taleo, Lever, SmartRecruiters), tracker, job matches | Unlimited autofill, tracker, resume builder | Simplify+ (AI tailoring, answers, Autopilot); reported $23.99–$39.99/mo | Market leader. Saves your answers to new questions for reuse. Widest coverage. |
| **Jobright** | AI job matching, autofill extension, resume tailoring, insider referrals, H-1B filter, "Agent" mode | Autofill and tracking | Turbo reported ~$20–40/mo; no public pricing page | Reviewers say it's more polished than Simplify but covers fewer sites, and **custom questions can come out wrong**. |
| **Huntr** | Kanban job tracker plus autofill extension | Unlimited autofill | Pro ~$40/mo | Tracker-first; 90K users, 4.8/5 rating on the Chrome Web Store. |
| **Careerflow** | Autofill, tracker, ATS resume checker, LinkedIn profile optimization | Basic autofill | Pro ~$20–40/mo | 200K+ extension users. |
| **Teal** | Job clipper and tracker; autofill in beta on select ATSs | Tracker | Teal+ ~$29/mo | Some reviewers say it has no real autofill. Strong at resume keywords. |
| **JobWizard** | AI autofill on 500+ platforms, cover letters, tracker | 30 autofills/week | Plus $19.99 / Pro $39.99 per 30 days | Markets "review, submit, done". |
| **OwlApply** | AI autofill, resume builder, ATS score | 10 autofills/month | Pro $22.99/mo | |
| **FrogHire** | Autofill that **remembers corrected answers**, multi-page flows | Free tier | — | Closest to Fillwell's "learning" pitch. Targets international students. |
| **FormFairy** | AI autofill for job applications and complex forms; learns details as you browse; data stays on the device | — | — | Local-first, privacy-focused. |
| **App Ditto** | Saves snippets and standard answers for job applications | — | In-app purchases | Small; manual snippet reuse. |

### 2. Auto-apply bots (find, fill and submit for you)

| Product | Approach | Reputation |
|---|---|---|
| **LazyApply** | Bulk applications on LinkedIn, Indeed and ZipRecruiter; from ~$99/yr | Poor Trustpilot reviews: applies to random jobs, makes errors, refund complaints |
| **AutoApplyMax** | Full auto-apply on LinkedIn Easy Apply, autofill elsewhere; $6.99–$22.99/mo | Aggressive comparison marketing |
| **LoopCV, AIApply, Scoutify, Tsenta, FastApply** | Matched auto-apply, at varying quality | Mixed |

The industry argument: Teal's CEO says auto-apply floods recruiters with unqualified applications. Automating LinkedIn Easy Apply can also risk your account. **Fillwell deliberately stays out of this category.**

### 3. Open-source projects (reference, not competition)

| Project | Notes |
|---|---|
| **AIHawk / Jobs_Applier_AI_Agent** (feder-cr) | ~29K GitHub stars, AGPL. Python/Selenium. Auto-apply has since been removed from the open-source version. |
| **ApplyPilot** | ~800+ stars, AGPL. Autonomous agent ("1,000 jobs in 2 days"). One fork has an "assist mode" that stops before submitting. |
| **AI Job Search** (MadsLorentzen) | ~18K stars, MIT. Full-pipeline local agent. |
| **applymatic** | Python, Playwright and the Claude API. Greenhouse and Lever fully supported; Workday in progress. Similar stack to Fillwell's. |
| **workday-copilot** | TypeScript/WXT Chrome extension for Workday using a local LLM. |

!!! warning "AGPL"
    Don't copy code from AGPL repos (AIHawk, ApplyPilot) into Fillwell unless Fillwell is also AGPL. Read them for ideas only.

## What's table stakes vs. what's differentiating

**Table stakes (must have just to be credible):**

- One-profile autofill on Greenhouse, Lever, Ashby and Workday
- Review before submit
- Save answers to new questions and reuse them
- Basic application tracking

**Where competitors are weak (Fillwell's opportunity):**

1. **Quality of written answers.** Competitors get custom questions wrong. Fillwell can ground every answer in facts the user stored, cite which facts it used, and refuse to invent experience. Measured with an eval suite.
2. **Transparency.** Show *why* a saved answer matched ("92% similar to 'Do you require sponsorship?', answered Oct 3") and let the user approve, reject or edit. Others are black boxes.
3. **An answer bank the user controls.** A real screen to browse, edit, merge and delete learned answers, with export and full deletion.
4. **Privacy.** Sensitive fields (EEO, sponsorship) are never sent to the LLM. Data is encrypted, with a local-first option. FormFairy competes on this point.
5. **Free AI without a $30/month paywall.** For a personal or portfolio project, this is free to offer.

## Recommended positioning

> **Fillwell: the honest, transparent autofill that learns your answers once and explains every fill.**

For interviews: *"Simplify and Jobright already do autofill at scale, so we didn't try to beat them on coverage. We focused on the parts they're criticized for: wrong answers to custom questions and black-box behavior. We built meaning-based question matching with a tuned threshold, answers grounded in the user's own data with citations, and an eval suite to measure quality."*

## Implications for the PRD

- **In scope:** Greenhouse, Lever and Ashby first, then Workday; the learning answer bank; grounded AI answers; explainable matches; review before submit.
- **Out of scope:** auto-apply, LinkedIn Easy Apply automation, job discovery and matching (Jobright's strength), resume builder.
- **Success metrics:** field-fill accuracy above 90% on saved test forms; zero wrong auto-fills from the answer bank in the test set; zero invented facts in generated answers (eval); under 3 minutes per Greenhouse application.
- **Before Phase 0 ends:** install Simplify and Jobright and use both on 5 real applications. Write down exactly where each one fails, especially on custom questions. That list becomes Fillwell's backlog and an interview story.

## Sources

- Simplify features and pricing: <https://help.simplify.jobs/articles/5623502-whats-included-in-simplify-features-and-pricing>
- Simplify autofill FAQ: <https://simplify.jobs/autofill>
- Simplify answer reuse (Adzuna review): <https://www.adzuna.co.uk/blog/?p=24409>
- Simplify review (Weekday): <https://www.weekday.works/post/simplify-jobs-review>
- Jobright review (Weekday): <https://www.weekday.works/post/jobright-ai-review>
- Jobright vs Simplify (Tsenta, competitor-written): <https://tsenta.com/blog/tsenta-vs-jobright>
- Jobright alternatives table (AutoApplyMax, competitor-written): <https://www.autoapplymax.com/compare/jobright-alternatives>
- Huntr extension: <https://chromewebstore.google.com/publisher/huntr-llc/ucdbb6bb1025d24433997aadff64d8c5e>
- Careerflow autofill: <https://www.careerflow.ai/job-autofill>
- Teal extension review (Resumly): <https://resumly.ai/best/best-job-search-chrome-extensions>
- FrogHire: <https://www.froghire.ai/blog/best-job-application-autofill-extension-workday-greenhouse-lever-icims>
- FormFairy: <https://extpose.com/ext/392880>
- LazyApply reviews: <https://tealhq.com/post/lazyapply-reviews> and <https://ca.trustpilot.com/review/lazyapply.com?page=3>
- AIHawk: <https://www.sourcepulse.org/projects/1831324>
- ApplyPilot: <https://www.sourcepulse.org/projects/24998881>
- applymatic: <https://github.com/shwechau/applymatic>
- GitHub topic, job-application-automation: <https://github.com/topics/job-application-automation>
