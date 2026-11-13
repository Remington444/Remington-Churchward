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

## 2026-11-06

**What I asked:** AI gap review of my Stage 2 spec (v1.1), then a revised draft with the fixes.

**What was produced:** spec v1.2 with 14 changes (see spec change log).

**Accepted:**
- Split the parity test into V5 (formula check, should pass) and V6 (stale-quote flag, expected to trip). Before: one check that would have looked like a model error. After: the expected failure is labeled.
- Added K_CALL / PREM_CALL. I had dropped them because the call doesn't hedge a receivable, but the contract needs all ten names.
- Added Tab architecture (nine tabs, including Checks) and a Validation rules section with expected values and tolerances.
- Added a Stage-4 source for every input and flagged placeholder values.
- Added the settlement date (2027-01-29) and the dollar consequence of a 5% euro decline to the problem statement.
- Used one name for money-market proceeds (`USD_MM`) and named step 2 `MM_USD_NOW`.
- Specified an XY scatter chart, exact winner labels with a tie rule, and named gray output cells.

**Rejected:**
- Parity tolerance as a fixed 0.5 cents: the two versions barely differ (0.5% of 1.1228 is about 0.56 cents, or about $13,500, against $12,000). Keeping my original percentage costs almost nothing in accuracy and makes the model more reusable.

**What was wrong and how it was caught:** AI review found my draft had no Tab architecture or Validation section, used two names for money-market proceeds, and set a parity tolerance that contradicted my brief.

## 2026-11-12

**What I asked:** Fix the instructor's 2026-11-07 feedback on Stage 2: make the parity tolerance say one thing, repair the V6 table row, write the capability README, and replace the bracketed template line in the 2026-11-06 entry.

**What was produced:** spec v1.3 with `PARITY_TOL` = 0.005 as a fraction of `FWD_PARITY` in §2.2, `PARITY_FLAG` and V6, plus a new named output `PARITY_GAP_PCT` (0.037876) that both test; a rewritten capabilities/fx-hedging/README.md with a scenario 30 row; the 2026-11-06 entry's bracketed lines replaced with the fixes I accepted (edited at the instructor's request).

**What was wrong and how it was caught:** When I applied my tolerance rejection, I changed §2.2 to 0.00050 USD per EUR and labeled it "about $12,000". That is 0.05 cents, which is only $1,200 on EUR 2,400,000. I also left V6 at 0.0050 and broke its table row with an unclosed backtick. The instructor caught all three; I checked the dollar figure (0.5% × 1.122773 × 2,400,000 = $13,473) before fixing it.

## 2026-11-12 (Stage 3 build)

**What I asked:** Execute Stage 3: build capabilities/fx-hedging/model.xlsx from my committed spec with an AI tool, following the build contract on the Stage 3 page.

**What was produced:** Before building, Claude compared spec v1.3 to the build contract and found two conflicts. The spec's color key had black formulas and green cross-tab links, but the contract requires green = formula and blue = assumption. The spec also said the call was not built, but the contract requires the call's premium in USD and its payoff as a function of S_T. Claude fixed both in spec v1.4 (committed separately), adding a Call tab as a payable illustration that is excluded from the winner labels. It then generated model.xlsx from v1.4 with ten tabs, 45 named ranges, 480 formulas and no typed numbers outside Inputs. The Checks tab has V1–V19, with expected values recomputed independently from the inputs. All checks pass except V6, which flags the stale forward quote as the spec expects.

**What was wrong and how it was caught:** On the first build, V1 (no error cells) reported one error. V1 scanned the Cover tab, and the Cover displays the check counts, which creates a circular reference. Claude narrowed V1's Cover range to skip the two check-count cells, recalculated, and V1 passed. My own audit of the workbook is still to do; findings go in the build-audit note.
