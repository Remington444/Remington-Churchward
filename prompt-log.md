# Prompt log

One entry per session that changed a file. Newest at the bottom. Never backfill, never edit a past entry.

## 2026-10-08

**What I asked:** Set up the Remington-Churchward portfolio repository: create the folder skeleton with placeholder READMEs, tailor AGENTS.md from the ai-conventions baseline and my resume, add the one-line CLAUDE.md pointer, write this first log entry, put my resume into RESUME.md as Markdown without changing its wording, and create .gitignore. Show me every file before I commit.

**What was produced:** The directory skeleton (capabilities/, docs/briefs/, docs/decisions/, data/, analysis/figures/), a one-line README.md in each otherwise-empty directory, AGENTS.md, CLAUDE.md, .gitignore, RESUME.md, and this entry. README.md did not exist, so it was created with the placeholder line "My bio goes here."

**What was wrong and how it was caught:** Nothing wrong found at generation time; I have not yet reviewed the files line by line. Three things were flagged for me to decide before committing: (1) RESUME.md contains my own phone and email plus the names, addresses, phone numbers and emails of two references, which conflicts with the "no personal data about anyone" rule in a public repo; (2) the resume's page-2 header (name, phone, email repeated) was left out of RESUME.md as a page-layout artifact; (3) spelling and wording in the resume ("Leaderships skills", "Google Suit") were left exactly as written, as instructed. Caught by the model re-reading the resume against the conventions page; still to be verified by me.
## 2026-11-6
**What I asked:** Find any gaps in my stage 2 spec.

**What was produced:** Detailed recommendations and fixes that needed to be done to my spec outline.

**What was wrong and how it was caught:** 
Rubric gaps (fix these first)
There’s no Tab architecture section. The spec says “Located at the top of the main worksheet,” which suggests a single sheet. The rubric requires named tabs with purposes, at minimum: Cover, Legend/Key, Inputs, one calculation tab per hedge (Forward, Money Market, Put), Sensitivity, and Notes & Assumptions. Without this, the Stage 3 AI will decide the layout itself.
There’s no Validation rules section. §6.3 is a checklist, but it has no expected numbers, and two required rules are missing: no error cells and every output is a formula. A rule the AI can check needs a value and a tolerance, for example “USD_MM = $2,694,656.09 ± $1.”
Your sections don’t follow the rubric’s eight. The rubric order is Problem, Inputs, Tabs, Assumptions, Calc flow, Sensitivity, Validation, Outputs. Yours follows the template instead, and §9 and §10 are template guidance text. That counts as filler against the 2–3 page limit, so delete both.
Two of the ten named ranges are missing. K_CALL (1.1187) and PREM_CALL (0.0199) aren’t in §2.1, and the call payoff isn’t specified anywhere. Add both rows, mark them as not used for a receivable, and give one line for the call payoff.
The §2.1 table has no Stage-4 source column and no placeholder flag. Each input needs its own source, for example: spot from Fed H.10, rates from CME Term SOFR and EMMI Euribor, forward and premiums from a bank or CME quote. Each value also needs the exact flag “indicative, replaced with live data at Stage 4.” The rubric also wants you to say that the strikes at spot and the two rates are values you chose.
The problem statement is missing two required items. It has no settlement date (inception date + 91 days) and no dollar consequence. Your brief had one: a 5% euro decline costs about $134,000 and hits grower payments.

