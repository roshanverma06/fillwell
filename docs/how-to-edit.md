# How to edit these docs

These docs are **docs-as-code**: Markdown files in the `docs/` folder of the Fillwell repo. The website rebuilds automatically every time a change is merged to `main`.

## Quick edit (in the browser)

1. Click **Edit this page** at the top right of any page.
2. GitHub opens the file in its editor. Make your change.
3. At the bottom: write a short summary, choose **"Create a new branch… and start a pull request"**.
4. Fill in the PR template (what changed, why).
5. A reviewer approves → merge → the site updates in about 2 minutes.

## Bigger edits (locally)

```bash
git checkout -b docs/FW-123-update-prd
pip install -r requirements-docs.txt
mkdocs serve                     # live preview at http://127.0.0.1:8000
# edit files in docs/ — the preview reloads on save
git commit -am "docs(prd): move personas to V2 (FW-123)"
git push -u origin HEAD          # then open a PR
```

## Rules

- **Who approves what** is set in `.github/CODEOWNERS`. ADRs, design docs and the PRD need the tech lead or product lead.
- **ADRs are never rewritten.** To change a decision, add a new ADR that "supersedes" the old one, and update the old one's status line only.
- **Big changes** (scope, dates, architecture) also get a line in the [docs changelog](changelog.md).
- **Update the "Last updated" / "Status" table** at the top of a doc when you change its meaning.
- **No secrets, real user data or personal info** in docs. Ever.

## Where the change history lives

| What you want to know | Where to look |
|---|---|
| Who last changed this page, and when | Page footer (automatic, from git) |
| Every change to a page, line by line | Click **View source** → **History** on GitHub |
| Who wrote a specific line | GitHub **Blame** view on the file |
| *Why* something changed | The pull request linked from the commit |
| Major changes at a glance | [Docs changelog](changelog.md) |
