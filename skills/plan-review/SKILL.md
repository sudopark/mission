---
name: plan-review
description: Use when a planning document written by another session — strategy.md, a campaign.md (campaign plan) draft or revision, or a DP/M opord (work instruction) draft — must be reviewed before the user's approval. Triggers on "first review requested" from a peer session, "review the plan (the other session posted)", "look at the campaign plan / work instruction (from that session)". Does NOT trigger on PR code review, host harness-file review, writing or fixing a plan directly (campaign and opord skills), checking a user report or PR text (report-review), or receiving and evaluating a completion report (campaign evaluation mode).
---

# Plan Review — First Review of a Plan Document

**This is the gate that plugs holes in a plan before approval.** A hole that surfaces after approval sends the plan back through a FRAGO or plan revision, and during execution it shakes code already written. The reviewer points out defects and gives a direction to fix them. The authoring session fixes the document.

## 1. Securing the target

- **Open the full text of the file.** Only the key block of an opord goes into the issue body (opord §7, Storage and mirror), so read `opord-*.md` under `<operations directory>/<issue number>/` in the authoring session's worktree. The operations directory is the host's artifacts directory (default `docs/operations/`, overridable in the host's `CLAUDE.md`). For campaign and strategy, the issue-body mirror is also the full text.
- **Open the parent document alongside.** For an opord, that is the DP's row in campaign.md item 9 (the owned-scope column is there); for a campaign, the campaign's row in strategy.md. If that strategy set up an owned-scope or limits item of its own beyond the template's 12 items, open it too.
- **For a campaign or strategy revision**, find the changed items from the revision line at the head. **An opord has no revision line at the head** — before approval there is no appendix D change directive yet, so use the previous round's list of findings as the changed places. Either way, apply perspective 5 of §2 to the whole document again — a new condition can collide with an unchanged one.
- **Count the volume format while reading** — is the head `■ Abstract` filled / is any table cell over three lines / do revision or change-directive numbers remain cited in the body / does the head revision line exceed one sentence (the usage notes at the bottom of the template are authoritative). Raise every hit as a finding, with "keep only what is currently settled and move the history down to the history item (appendix D for an opord)" as the direction. Unlike the six perspectives this is a mechanical check that takes no judgment, so it does not go through the §3 gate.
- **For a revision, sweep the three categories yourself** (the campaign skill's plan-revision sweep in evaluation mode is authoritative) — **quantities derived** from the changed value, **paths that take that object as their destination** (sequel, decision point, branch, start condition), and **other items stating the same fact**. If the authoring session sweeps by word, all three leak, and the leaked places are not in the revision line, so looking only at the changed items will not show them.

## 2. Six perspectives — run all, and leave a result line per perspective

| # | Perspective | Judgment question | Method |
|---|---|---|---|
| 1 | Parent alignment | Did this document take over, as given, what the parent gave this DP or campaign — content, predecessor, owned scope, size? If the parent specified a procedure ("by skill X §N"), is that requirement in this document? | Compare item by item against the parent row |
| 2 | Cited clause original text | Is any requirement item missing from the rules or skill clauses that this document or its parent points to as binding? | Open the clause's **original text** and, for each requirement item, write "which item or step in the document does this". If no corresponding item or step can be named, it is missing |
| 3 | Pre-research facts | Do the file-and-line citations, behavior claims, and paths match reality? If the same value is written in two units or scales, do they cover the same range once converted? | Verify against the pre-research output and the host's file conventions. If not all can be checked, check the claims the conclusion leans on first. For a value split across units, compute the converted value and compare — each notation reads naturally in its own place, so neither the word nor the value catches it |
| 4 | Verification means exists | For each task, "what catches it if this change breaks?" Do the tests, snapshots, or on-device items named as substitute verification **actually pass through this change's code path**? Does verification attach to the likeliest and riskiest failure mode the document itself names? | Follow the path the named test draws or calls, in code |
| 5 | Conflicts between conditions | In the world where a decision point or branch fires, can the phase exit condition and MOE still be met? Does a branch remove an output that a later DP or indicator uses? Does a ratio or threshold judgment lump together targets of different nature? | The scenario check below |
| 6 | Placement of new elements | Could a document, file, type, or scheme this plan newly creates fork from a canonical source already covering the same subject? Does the plan fix only one of a pair of locations the host's file conventions say go together? | Verify against the host's file conventions and the pre-research output: find the existing canonical source for the new element's subject, and check whether the plan fixes that side too or points to it |

**Input contract.** Perspectives 3 and 6 expect the pre-research output (the facts, paths, and existing canonical sources found before planning) and the host's file conventions as input. They are provided by the host project's intake/execution skills (a later stage of this plugin ships a default). Where either is absent, open the files and sources directly and say so in the result line.

**Perspective 5 scenario check** — for every decision-point, branch, and exception-handling row, walk both directions. Checking only that the format fits (the IDs are paired) means this perspective was not run.

0. **Enumerate the targets first.** Write the ID of **every row** of the decision-point table, branch table, and exception-handling table — rows marked `standing` count too. The scenario-check lines are 1:1 with this list. A missing ID means perspective 5 was not run.
1. **Find what disappears from the sentences.** Actually run `grep -n '<ID>' <target file>` and write every hit line number in the evidence cell of the scenario-check line. Read **every sentence** among the hits that states the on-trigger action (DP row content cell, branch cell, sequel). If one says "do not set up this DP" or "discard", list one by one the **concrete outputs** that DP was producing (targets, files, tests, spec sections). A concept-level summary such as "the entire derived access" is not an output.
2. **Read the remaining conditions literally.** Substitute the phase exit condition, sequel, and MOE indicator sentences into the triggered world and see whether each can be met as written. If a decision point routes only part of the targets down another path while the exit condition judges all targets by one standard, that is a conflict.

```
<ID> [grep: <all hit line numbers>] triggers → disappearing outputs: <concrete list — location of the supporting sentence> | where they are used: <exit condition, later DP, MOE indicator> | exit condition in the triggered world: can be met / <which sentence cannot be filled>
```

**Comparison lines for perspectives 2 and 6** — these two fill the lines below instead of judging. A line whose right-hand cell is `none` or `no` is a finding candidate.

```
Perspective 2: <clause location> "<requirement text verbatim>" → <the item or step in this document that does that job under that requirement's name | none>
Perspective 6: new <file or document> ↔ existing canonical <path §> → does this plan fix the existing side or make it point to the new one: <yes — task location | no>
```

For perspective 2, write **every** requirement sentence of the cited clause, one line each — none of the sentences containing "must", "required", "shall", or "every" is skipped. In the right-hand cell write **only an item or step with the same name as the requirement**. Do not write that a differently named item "in effect plays that role". **A line whose right-hand cell is `none` skips §3 gates 2 and 3 and is a finding straight away.** If the requirement looks inapplicable to this plan, the reviewer does not decide the exemption; put it in the finding as "ask the user at approval". For perspective 6, even if the new document looks to have split roles with the existing canonical source, it is `no` when the plan has no pointer for a reader of the existing source to reach the new document.

Leave one line per perspective — `Perspective N: no issues (<what was checked>)` or the finding numbers. A perspective with no line was not run.

| When tempted to move on with this | In fact |
|---|---|
| "Requirement X is effectively absorbed by another item" | If the requirement's name is not in the document, it is missing. The cost to the authoring session of adding one cell is small, and if it is approved without it the executor never learns the requirement |
| "That requirement's axis does not hold for this plan" | Exempting a clause is not the reviewer's authority. Even if the exemption looks right, raise it as a finding and let the user decide |
| "The new document and the existing one have divided roles" | Divided or not, if the existing side does not point to the new side, a reader of the existing side cannot find the new document |
| "The decision-point and branch IDs are all paired" | Pairing is form. Follow, sentence by sentence, the outputs that vanish after the trigger |
| "There is a default action when undecided, so it is absorbed" | If another row handles the same event differently, the executor does not know which to follow. The existence of a default does not remove the conflict |

## 3. Finding gate

Before raising a finding, it must clear three checks. If it fails any one, do not raise it.

1. **Observable consequence** — what goes wrong if the approved plan is executed as written (missing output, verification gap, unreachable exit condition, wrong path)? "Possible in theory" is not a consequence.
2. **Not already absorbed** — do not other items, the parent document, or existing code already plug that hole? Confirm by grep or opening the file. To rule it absorbed, you must be able to **name by location the item, step, or code that does the job.** If a clause-required item is absent and "a similar structure effectively stands in", that is not absorption but a miss.
3. **Does it conflict with intent** — if the document states the authoring session's reason for deciding it that way, read that reason first. If the reason stands, it is not a finding. **The exception is a `none` line of perspective 2** — a judgment to exempt a clause requirement is not closed by the reviewer even when a reason is written; it goes up as an approval agenda item.

**A decision is not a finding.** Where the user is to decide — work line, end state, lineup, DP order — the reviewer does not pick for them; pass it on as "ask the user at approval".

## 4. Delivering the result

Send to the requesting session (or the user). Format:

- First line: **pass** or **N revision requests**.
- Per finding: document location (item or task) → evidence (file and line, or clause original text) → the consequence that goes wrong → direction to fix.
- Even on a pass, attach one or two lines of the key facts confirmed — so the authoring session knows what was verified.

**Re-review** — on receiving the revised version, check in the file only the places pointed out and the items that revision touched. A correction that only fixes a fact, such as a wrong path, is accepted without re-review.

**After a pass, go to the user's approval.** The authoring session takes the approval directly from the user.

After sending the result, tell the user only that case's conclusion and what is newly theirs to do, in a line or two. The full list of what remains is given only when the user asks (same as the report-review skill).

## 5. End record — skill-end

When one document's review ends in a pass, or the requester withdraws the review, the skill has finished its work. A revision-request → re-review round is the same run — fire once at the moment of the pass.

> Extension point: `skill-end` fires here. See `docs/extension-points.md`.
