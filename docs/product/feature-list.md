# Fillwell: Combined Feature List

*November 14, 2026. Brings together the core features, the competitor-gap features, the unique ideas, and the resume-aware fill requirement. The **Version** column follows the release plan in `product/prd.md` §5, which is the source of truth.*

**Product theme:** *Autofill you can trust. Answer once; Fillwell remembers and explains every fill.*

**Tiers**
- **Core:** the minimum to be a credible autofill tool (table stakes)
- **Signature:** what makes Fillwell different; the interview stories
- **Later:** good ideas parked until after launch

---

## Tier 1: Core

| # | Feature | What it does | Version |
|---|---|---|---|
| C1 | **Profile & knowledge base** | Personal facts, education, work history, projects, links and certifications, entered once | V1 |
| C2 | **Resume upload & parsing** | Builds the profile automatically from a PDF or DOCX resume | V1 |
| C3 | **Form detection & autofill** | Reads every field (label, type, options) and fills it | V1: Greenhouse, Lever, Ashby · V2: Workday · V3: iCIMS, SmartRecruiters |
| C4 | **Ask once, never again** | Unknown question → ask the user → save → reuse. Matching by exact text, then by meaning (embeddings) | V1 |
| C5 | **Dropdown / radio option matching** | Fits a saved answer to each site's options ("Yes" vs. "Yes, I am authorized") | V1 |
| C6 | **AI answers for descriptive questions** | "Why this company?" etc., generated from the user's own data plus the job description | V1 |
| C7 | **Review before submit** | Never auto-submits. The user always clicks Submit | V1 |
| C8 | **Basic application log** | Records each application (company, role, date, link, resume used) | V1 |

## Tier 2: Signature

### A. Trust & transparency
| # | Feature | What it does | Version |
|---|---|---|---|
| S1 | **Answer citations** | Every AI answer shows which stored facts it used; click to see the source | V1 |
| S2 | **Honesty guard** | A second check flags any claim not supported by stored facts. Never invents skills; names real gaps instead | V2 |
| S3 | **Confidence levels** | Green: fill automatically; yellow: fill and highlight; red: ask the user. Shows *why* ("94% match to the question you answered Oct 3") | V1 |
| S4 | **Fill preview** | A side-panel table of every field, the value to be used, and its source, before anything is written | V1 |
| S5 | **Source tags on fields** | Each filled field shows where its value came from ("from RoshanVerma_Tesla_DE.pdf" / "from default resume" / "from answer bank") | V1 |

### B. Resume-aware, non-destructive filling
| # | Feature | What it does | Version |
|---|---|---|---|
| S6 | **The uploaded resume wins** | Order of precedence: resume attached to *this* application → default resume → base profile. Applies to resume-type fields only (experience, education, skills, summary). Personal and legal facts always come from the profile | V1 |
| S7 | **Never overwrite** | By default, fills empty fields only. Values already there (typed by the user or parsed by the site) are kept; differences show as an accept-or-ignore suggestion. "Overwrite all" must be switched on explicitly | V1 |
| S8 | **Upload-after-fill re-sync** | Notices a resume attached after autofill and offers to re-fill only the fields Fillwell filled itself | V2 |
| S9 | **Per-application resume data** | Parsed resume data is kept only for that application (it survives multi-step pages), cached by file fingerprint, and offered as a saved variant | V1 |

### C. Smarter memory
| # | Feature | What it does | Version |
|---|---|---|---|
| S10 | **Expiring answers** | Time-sensitive answers (student status, start date, years of experience, visa status) are re-confirmed on a schedule or when the profile changes | V2 |
| S11 | **Consistency checker** | Warns when a salary expectation or start date conflicts with what you told the same company before, or with another field on the same form | V2 |
| S12 | **Knock-out question detector** | Flags screening questions likely to auto-reject (years of experience, sponsorship, on-site requirement) and helps answer honestly but precisely | V2 |
| S13 | **Personas** | Data Engineer / Analytics Engineer / SWE / Data Analyst. Picked automatically from the job description; shared facts, persona-specific framing, resume and answers | V2 (V1 has one default persona) |
| S14 | **Answer bank manager** | V1: list, search, edit, delete · V2: merge near-duplicates, version history | V1 basic · V2 full |

### D. Privacy
| # | Feature | What it does | Version |
|---|---|---|---|
| S15 | **Sensitive-field rules** | EEO, sponsorship and similar follow user-set rules, are never sent to the LLM, and are encrypted at rest | V1 |

### E. After you apply
| # | Feature | What it does | Version |
|---|---|---|---|
| S16 | **Application snapshot vault** | Saves the job description, every answer and the resume version per submission, since postings disappear after they close | V2 |
| S17 | **Duplicate-application guard** | Warns when you've already applied to the same role, including across job boards | V3 |
| S18 | **Resume pipeline connection** | Recognizes `RoshanVerma_Company_Role.pdf` files from the LaTeX pipeline and links or attaches the tailored resume for that job | V3 |

## Tier 3: Later

| # | Feature | What it does | Version |
|---|---|---|---|
| L1 | **Length-aware writing** | Reads the field's character limit; short, medium and long versions | V2 |
| L2 | **Teach mode for fields** | Click an unrecognized field and label it; remembered for that site | V3 |
| L3 | **Save & resume long forms** | Saves progress through multi-page Workday flows; restores after a crash | V3 |
| L4 | **Workday account tracker** | Tracks which companies you have accounts with; hands passwords to the browser's password manager (never stores them) | V3 |
| L5 | **Learning from edits** | Learns tone and length preferences from before/after edits of generated answers | V4+ |
| L6 | **Interview prep from snapshots** | Turns a saved application into "what you told them" prep notes | V4+ |
| L7 | **Personal stats** | Questions answered, % filled automatically, hours saved | V3 |
| L8 | **Results analytics** | Which personas, resumes and answers got callbacks (shown as a hint, not statistics) | V4+ |
| L9 | **Follow-up drafts** | A reminder plus a follow-up note that uses what you said in the application | V4+ |
| L10 | **Speak your answers** | Speak, transcribe, tighten to fit, save to the answer bank | V4+ |
| L11 | **Local-only mode** | Bring your own model (e.g., Ollama) so nothing leaves the machine | V4+ |
| L12 | **Shared question dictionary** | Opt-in, anonymized sharing of question wording (never answers) to improve matching | V4+ |
| L13 | **Open-source release** | Public code; decide license before launch | V4+ (license decision before V3) |

## Out of scope (by design)

- Auto-submit or auto-apply bots
- LinkedIn Easy Apply automation (against LinkedIn's terms; account risk)
- Job discovery and matching (Jobright's strength)
- Resume builder (already covered by the LaTeX pipeline)
- Getting around CAPTCHAs

## Demo stories

**MVP demo (V1):**
1. Attach a tailored resume and click Fill → **the uploaded resume wins (S6)**, nothing overwritten (S7), every field tagged with its source (S5), preview first (S4).
2. A new question appears → asked once → the next application **fills it at the green tier with an explanation (S3)**.
3. "Why this company?" → a **cited answer (S1)** built only from your facts.
4. "Will you require sponsorship?" → follows your rule; **never sent to the AI (S15)**.

**Full demo (from V2):** adds the honesty guard (S2), knock-out flags (S12) and the snapshot vault (S16).
