# AI conventions
change anything in this thats says i am a business management major to a business finance major
## About this repository
Public portfolio repository of Remington Churchward, a BBA in Business Management student (Shidler College of Business, University of Hawaii at Manoa), built for a business course.
Canonical file: AGENTS.md. CLAUDE.md points here.

## Field
Business management. My coursework covers written business communication, statistical analysis, business information systems, behavioral management, and financial and managerial accounting. I work in Excel, Google Sheets, and the rest of Microsoft Office 365 and Google Workspace. My work experience is in restaurant service (Deck Waikiki).

## Where things are
- capabilities/<capability>/  a capability, with its README.md, spec.md and model file
- docs/briefs/          written BEFORE work: scope + hypothesis
- docs/decisions/       written AFTER work: recommendations
- analysis/             findings and figures
- data/                 sourced inputs, with provenance

## Naming
- The directory matters most. A file in the wrong folder is harder to find. If you are not certain which folder a file belongs in, ask me
  before you write it — do not choose for me.
- Graded files use the exact filename the stage brief gives — lowercase,
  hyphens, no spaces. Dated documents are YYYY-MM-DD-slug-type.md (no name — the repo is yours);
  the stage page says so when they do.
- Slugs name the engagement, never the week, the course, or the assignment
  number.
- Never invent a path or a filename. I will give you the exact one.

## How I work
- Explain concepts fully and walk the worked example. Do not hand me conclusions.
- Explain in plain business terms first, then the math or the formula. When a calculation is involved, show how to build it in Excel or Google Sheets, since that is where I work.
- Critique my reasoning directly. I would rather be corrected than agreed with.
- When you are uncertain, say so and say what would resolve it.

## What you may and may not draft
- You MAY explain, critique, debug, quiz me, and draft mechanical files.
- You MAY NOT write my briefs, analyses, memos, or reflections.
- Every statistic or figure you give me is a draft until I verify it against a source.

## Never paste into a model
The repository is public, and the same rule governs what goes into a chat window. If it would not be safe in a public repository, it does not go into a model. From my own work, that means:
- Anything about Deck Waikiki's customers: names, reservation or contact details, payment card numbers, receipts, order or point-of-sale records, tab histories.
- Deck Waikiki's internal business data: sales figures, pricing and cost information, menus not yet public, vendor terms, schedules, tip pools and payroll.
- Personal information about Deck Waikiki's managers and coworkers, including names tied to schedules, pay, performance or disciplinary matters.
- Anything about my employer's operations that I learned on the job and that falls under a confidentiality expectation.
- Student records: my own or a classmate's grades, student ID numbers, advising records, and classmates' graded work or personal details.
- Course material I do not own: textbook chapters, publisher slides, paid datasets, and instructors' unpublished exams or solutions.
- My references' contact details (name, phone, email, address) and any other private contact details for other people.
- Credentials, API keys, tokens, and passwords of any kind.
If I paste something that fits this list, stop and tell me rather than continuing.

## Documentation
When work changes, update the document that describes it in the same commit.
A capability's README names the engagements that exercised it — keep that current.

## Scope
Do the work I asked for. If you notice something worth doing that I did not ask
for, tell me instead of doing it.

## Commits
Descriptive messages: what changed and why. Never "update" or "stuff".

## Prompt log
At the end of every session that changed a file, append one entry to prompt-log.md: the date, what I asked, what you produced, what was wrong and how it was caught. Never backfill earlier sessions and never edit a past entry.

## Never include
No credentials, no API keys, no personal data about anyone, no licensed or
copyrighted material. If I paste something that fits that description, stop and
tell me rather than committing it.

## Mistakes to avoid (append to this list)
Record errors here as they happen, so the same one does not repeat.
- (empty — add the first one when it happens)
