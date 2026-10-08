# Prompt log

One entry per session that changed a file. Newest at the bottom. Never backfill, never edit a past entry.

## 2026-10-08

**What I asked:** Set up the Remington-Churchward portfolio repository: create the folder skeleton with placeholder READMEs, tailor AGENTS.md from the ai-conventions baseline and my resume, add the one-line CLAUDE.md pointer, write this first log entry, put my resume into RESUME.md as Markdown without changing its wording, and create .gitignore. Show me every file before I commit.

**What was produced:** The directory skeleton (capabilities/, docs/briefs/, docs/decisions/, data/, analysis/figures/), a one-line README.md in each otherwise-empty directory, AGENTS.md, CLAUDE.md, .gitignore, RESUME.md, and this entry. README.md did not exist, so it was created with the placeholder line "My bio goes here."

**What was wrong and how it was caught:** Nothing wrong found at generation time; I have not yet reviewed the files line by line. Three things were flagged for me to decide before committing: (1) RESUME.md contains my own phone and email plus the names, addresses, phone numbers and emails of two references, which conflicts with the "no personal data about anyone" rule in a public repo; (2) the resume's page-2 header (name, phone, email repeated) was left out of RESUME.md as a page-layout artifact; (3) spelling and wording in the resume ("Leaderships skills", "Google Suit") were left exactly as written, as instructed. Caught by the model re-reading the resume against the conventions page; still to be verified by me.
