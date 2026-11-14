# Fillwell: Privacy & Data Handling

| | |
|---|---|
| **Status** | Draft v0.1 (internal). Public privacy policy to be derived from this before the V3 launch |
| **Owner** | Tech lead + product lead |
| **Last updated** | November 14, 2026 |

## 1. Principles
1. **Collect the minimum.** Only data needed to fill applications.
2. **The user owns the data.** Export anytime, delete everything anytime.
3. **Sensitive data never goes to an AI.** EEO, disability, veteran, sponsorship, work authorization and salary answers follow user rules only.
4. **No selling, no ads, no training** on user data.
5. **Be transparent.** Every filled value shows where it came from.

## 2. Data inventory

| Data | Where stored | Why | Sent to LLM? | Retention |
|---|---|---|---|---|
| Google account id + email | `users` | Sign-in | No | Until account deletion |
| Profile (identity, education, work history, skills) | `profiles`, `knowledge_items` | Autofill, grounding | Only relevant **non-sensitive** fact snippets | Until deleted by the user |
| Resume files | S3 (encrypted) | Parsing, attaching | Extracted text only, for parsing | Until deleted |
| Parsed resume data | `resumes.parsed` | Precedence filling | No (already structured) | Until deleted |
| Answer bank | `answers`, `question_variants` | Never ask twice | Non-sensitive question text for option mapping only | Until deleted; expiring answers prompt for re-confirmation (V2) |
| Sensitive answers | `answers` (`sensitive=true`, encrypted) | User rules | **Never** | Until deleted |
| Job description text | `applications.jd_text` | Generation; snapshot vault (V2) | Yes, as untrusted context | 12 months, then removed (configurable) |
| Filled values per application | `application_fields` (encrypted) | Log, consistency checks (V2) | No | 12 months |
| Generated answers | `generations` | Review, quality metrics | — (they are LLM output) | 12 months |
| Form field descriptors | Not stored (request only) | Build fill plan | No | Not stored |
| Logs / metrics | CloudWatch, Sentry | Operations | No | 30 days; **no personal data** |
| LLM traces | Langfuse | Quality debugging | — | 30 days, sensitive data removed |

## 3. Third parties (sub-processors)

| Provider | Purpose | Data shared |
|---|---|---|
| AWS | Hosting, database, storage | All stored data (encrypted) |
| LLM provider (see ADR-004) | Resume parsing, answer generation, option mapping | Resume text (parse); non-sensitive fact snippets + job description + question (generate) |
| Google | Sign-in | Standard sign-in info |
| Sentry | Error tracking | Stack traces with personal data scrubbed |
| Langfuse | LLM tracing | Prompts/outputs with sensitive data removed |

Choose LLM API settings that **don't use our data for training** and that keep data for the shortest time available.

## 4. User rights (MVP features)
- **Export:** `GET /me/export` returns all user data as JSON (plus resume files).
- **Delete:** `DELETE /me` permanently deletes the database rows and S3 objects within 24 hours; the audit log keeps only the fact that a deletion happened.
- **Edit:** profile, answers and rules are editable in the side panel.

## 5. Chrome Web Store requirements (for V3)
- A public privacy policy URL
- Data-use disclosures in the developer dashboard (personally identifiable info, website content)
- An explanation for each permission
- Following Chrome's "Limited Use" rules for user data

## 6. Open items
- Decide the job-description retention default (12 months proposed).
- Get legal or mentor review of the public privacy policy before V3.
