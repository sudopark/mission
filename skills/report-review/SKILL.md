---
name: report-review
description: Use when a subordinate session sends the controlling session a draft it is about to show the user or publish — a completion report, decision request, analysis result, or the commit messages and PR body drafted before PR creation. Triggers on "vet the user report", "can I send this report out?", "look at the commit messages and PR body", and peer messages headed `[Report review]` or `[PR copy review]`. Does NOT trigger on plan-document review (plan-review skill), PR code review, announcing the gates to subordinate sessions (control-brief skill), or this session answering the user directly.
---

# Report Review — Vetting User Reports

**Only a report that has passed the controlling session goes out from a subordinate session to the user.** A subordinate session, trying to tell everything it found, reports facts with no consequence and inflates trivial things. The user reads through the noise and misses the point. One example: a session attached "the campaign assumption loses its basis — worth recording in the ledger" to a test the user had already decided to delete, and spent more than a paragraph on a column-count difference whose conclusion was "it doesn't matter".

## 1. Targets

There are two.

- **User report** — a message a subordinate session **stops and hands over to the user**: a completion report, a decision request, an analysis or investigation result. A one-line progress note during work and an immediate answer to a user question are not covered. An immediate answer that carries an analysis result and asks for the user's judgment is covered. The criteria are in §2. **An approval request is covered only up to the first three summary blocks** — opord §3-8 has the full plan follow them verbatim, and plan-review has already seen the full plan. Count criteria 2 and 3 at the end of the three blocks. The body of a formal report whose format a template fixes (completion report, start report, progress report, immediate report) is not covered.

**A `[Report review]` request carries three things with the draft** — the user's question verbatim (if the report answers a question), whatever the user has already decided that the draft touches, or `none` if nothing. Criteria 1, 6, and 7 are judged by these attachments alone. If an attachment is missing, reject with `attachment missing`.
- **PR copy** — all the branch's commit messages and the PR title and body. Received **once, before PR creation** — receiving per commit would stop every task. The authoring session sends, under a `[PR copy review]` header: the output of `git log --format='%B' <base branch>..HEAD`, the draft PR title and body, one result line for each of the host's pre-PR checks, one line of the parent issue's background, and whether a work instruction exists. A commit message that is rejected is rewritten before push. If a review round after the PR is public changes a message or the body, only the changed parts are received again. The criteria are in §2-1.

## 2. User-report criteria — all must pass

| # | Criterion | Rejection signal |
|---|---|---|
| 1 | **The first line is the conclusion** | The first line starts with background, history, or "I checked". If the user asked a question, the first line does not answer it |
| 2 | **The last line is a conclusion too** | The user reads from the bottom. The last line is neither a conclusion or status (`done` / `remaining: …` / `awaiting user verification: …`) nor a question to the user (the last item of a numbered list, if several). It ends with grounds or an aside |
| 3 | **Length cap** | The draft is over 15 lines as written. Blank lines, table separator lines (`|---|`), code-fence lines, and the `[Report review]` header line are not counted; table rows and code lines are. The only exception is an answer where the user asked for detail |
| 4 | **No fact without a consequence** | The draft has no sentence saying what action or choice the fact leads to. A paragraph ending in "so it doesn't matter" or "no impact" is the typical case |
| 5 | **Weight matches the consequence** | Bold text, "the basis is missing", "worth recording", or "caution" is bigger than the actual consequence. If the consequence is one line in the ledger, it is nothing to emphasize |
| 6 | **No unasked expansion** | It adds a proposal outside the scope the user set, or "if we also do X, then Y". If there is a real reason to widen the scope, it goes out as one separate question |
| 7 | **Do not shake what is settled** | It raises again a side effect of something the user already decided. A defect that would change the decision must come with its evidence of having passed the host's defect-reporting gates: an observable consequence for the user, no existing guard already absorbing it, and an understanding of why the code is written that way |
| 8 | **Grounds only as needed for the judgment** | It carries line numbers, test names, file lists, or tables the user does not use to decide |
| 9 | **Questions only the user can answer** | It asks about something the session can settle itself. It has more than three questions |
| 10 | **Sentence norms** | A violation of the host project's writing norms — broken sentences, translationese, passive voice, abbreviations, preachy closings. Absent host norms, the reviewer's judgment applies |

### 2-1. PR copy criteria

Apply 4–8 and 10 of §2 as they are (reading "user" as "reviewer"), and add the following. Criteria 1–3 (first and last line, 15-line cap) do not apply — commits and PRs have their own formats. The source of these criteria is the host project's commit and PR norms.

| # | Criterion | Rejection signal |
|---|---|---|
| P0 | **Results of the host's pre-PR checks are attached** | A result line is missing for one of the pre-PR checks the host defines. Whether a result is right or wrong is not examined — only whether the line is there |
| P1 | **The first line is the behavior change** | The commit's first line is `[#issue] list of files or classes`, or says only what was done, like "fix", "improve", "refactor" |
| P2 | **Anchor-per-bullet** | A body bullet has no anchor at its head (the anchor is a code symbol; for a change where no symbol changed, a file path — docs, manifests, configuration), or has several anchors at its head. Symbols that appear inside the explanation are not counted. **A bullet that carries grounds, background, or the reason for a choice** — judged by content, not by line count. That belongs in the title; a constraint that neither the title nor the code shows goes in one paragraph at the bottom of the body, and **that paragraph is not a bullet and is outside this criterion** |
| P3 | **Final decisions only** | It contains a rejected alternative, review history, trial and error, or "it used to be X". Traces of review feedback ("per the review") count the same |
| P4 | **No repeated background** | The same content as the one-line parent-issue background in the request is written again in every commit |
| P5 | **The PR body is top-down, and below it a problem → approach narrative** | The first paragraph starts with the problem or background, and what this PR changed comes later. It is a file list. There is an empty "remaining work" section. For a run with a work instruction, the completion report sections for items 1, 3, and 10 are missing (the host's PR conventions, body) |
| P6 | **Volume is the minimum needed to understand** | The same content repeats across the narrative, sections, and commits. Tables or sections go unused for understanding |
| P7 | **The PR title carries the coordinates** | It is a campaign DP's PR but the title has only `[#issue]`, without the parent campaign issue and the DP number (`[#issue · #campaign DP-x.y]`) (the host's PR conventions, title) |

**The only basis for judgment is what the authoring session sent.** Apply the criteria using only the draft and the information in its request message. Do not open that session's conversation log, memory, or progress files to pry out hidden circumstances, and do not bring in context the controlling session knows separately about that session (campaign phase, ledger, next DP, CI state) to add content with "write this too" or "the next is X, so change the last line". Whether the content is true, and which facts are missing, is the authoring session's job; this gate filters the form the user reads. Where the size of a consequence has to be seen, as in criteria 4 and 5, judge within what was sent — if the consequence is not visible, write only "the consequence is not visible".

In one past case, a review brought in a campaign's next DP and its `/clear` rule from the controlling session's own knowledge and had the last line changed, and the user corrected it.

## 3. Reply

Send it with the messaging tool to the requesting session. The format is one of two.

- `[Report review passed]` — show it to the user as it is (for PR copy: raise the commits and PR as they are). If what needs fixing is trivial (one word, a particle), give the fixed sentence with it and pass.
- `[Report review rejected: N items]` — one line per item: `criterion number (for PR copy, including the P number) → quote of the offending sentence → direction of the fix`. Re-review looks only at the rejected places.

A pass is only permission to output. It does not stand in for the user's approval or merge approval, and it does not stand in for the receiving session's permission prompt.

After replying, tell the user only that case's conclusion and what is newly theirs to do, in a line or two. The full list of what remains is given only when the user asks — repeating the list in every notice only adds to the reading, and finished items keep appearing so that the list drifts from reality.

## 4. End record

When one report (for PR copy, one PR's bundle of copy) ends in a pass or the requester withdraws it, fire the declaration below. A reject → re-review round is the same run.

> Extension point: `skill-end` fires here. See `docs/extension-points.md`.
