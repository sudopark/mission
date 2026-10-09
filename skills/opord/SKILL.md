---
name: opord
description: Use when writing a work instruction for a single-PR issue or one DP — on transition from intake, on explicit `/opord` invocation, and at any point where superpowers:writing-plans would be invoked (including the end of brainstorming). Triggers on "write a work instruction", "make a plan", "opord", and the moment writing-plans would fire. Does NOT trigger on campaign or strategy authoring (campaign), execution (implement, orchestrate), or issue decomposition (intake).
---

# Opord — Writing the Work Instruction

## 1. Overview

**The work instruction is the plan.** It **replaces** superpowers:writing-plans. The document structure is driven by `${CLAUDE_PLUGIN_ROOT}/templates/opord.md`. What survives from writing-plans is only the task-step format of appendix A (`### Task N:` headings, Files, Interfaces, checkbox Steps) and the No Placeholders principle. Do not use writing-plans' header, Execution Handoff, or save path.

The work instruction is a **handoff document another session's agent can start from using this document alone.** The executor starts without this session's conversation or exploration memory — only what is written down.

Common template rules: no prose outside the template's items / fill inapplicable items with "none" / **write tersely**: keep only the core of each item and strip repetition, background padding, and ornament — what shrinks is sentences, never the decisions or information they carry / sentence norms are governed by the host project's writing norms (absent host norms, the reviewer's judgment applies) — terseness operates within those requirements. Terms (DP, LOE, FRAGO, MOP/MOE, PIR/FFIR) are defined in the template's terms line.

The host's operations directory (default `docs/operations/`, overridable in the host's `CLAUDE.md`) holds the plan and progress files this skill reads and writes; below it is called "the operations directory". See `docs/extension-points.md` for the host contracts.

## 2. Entry

- The unit is **one M issue or one DP** = one PR. S (verbal order), L (campaign), and XL (strategy) are outside this skill.
- If the host has an intake skill and it has not run, invoke it first — pre-research (1-a) reuses the intake exploration; otherwise proceed from the issue body.
- Input: for **M**, the pre-research brief (including the list of undecided decisions). For a **DP of an L**, the DP ID plus the `campaign.md` quotes (1-c).

  This skill expects the pre-research brief as input. It is provided by the host project's intake skill (a later stage of this plugin ships a default).
- **Scope-clarity precondition** — a work instruction is written only in an area certain enough that no follow-up issue split is needed. If, while writing, you cannot settle the tasks, the boundary will not draw, or a "try it and see" step becomes necessary, **stop writing and return to the §3-5 question round to settle what is undecided.** If the boundary still will not draw after settling, the size was misjudged — send it back to intake for re-judgment (promotion). Handing a vague-scope order to the executor produces frequent contingency questions.

## 3. Procedure

1. **Confirm input** — the DP ID plus campaign.md quotes; for M, the pre-research brief.
2. **Check start eligibility (DP only)** — campaign plan approved + predecessor DP merged + owned scope secured. If any is missing, do not start. The sole exception is a dependent DP for which the user explicitly allowed stacking — "predecessor DP merged" relaxes to "predecessor DP PR exists + interface contract settled" (the stacked-DP exception in the host's orchestration skill). **If the predecessor DP is `regressed` in the ledger, "predecessor DP merged" is not satisfied even with a merge history** — wait for the re-run or get a user decision.
3. **Pre-research** — reuse the intake exploration; if insufficient, use a code-analysis subagent.
4. **Derive tasks** — derive the stated tasks (DP: campaign.md item 9 content + the interface contracts of item 8's inter-task commitments / M: the issue body and pre-research brief) and the implied tasks (not stated but pulled in by the host's rules and architecture — paired locations, test targets, localization keys, and the like), and set the item-2 mission statement from the essential tasks (those whose omission leads directly to missing the end state). If an implied task exceeds the owned scope or delegation, put it in the question round. **If no governing doctrine for pulling in an implied task exists in the host's rules or precedent, ask the host to codify the rule** — planning does not stop; carry the request in the §3-5 question round.
5. **Questions** — **the pre-research brief's "only the user can answer" undecided list is the first input** (this is where they get settled, not in intake) + nuance of purpose · end-state details · what must not be touched · narrowing of delegation · accepted risks · immediate-report conditions. Ask them **in one batch** with AskUserQuestion, each with a default — for undecided decisions where the design forks, attach **two or more options with trade-offs** instead of a single default (recommended option first).
6. **Draft** — the usage notes at the bottom of the template are authoritative: writing order · 3-a ≥ 3-c · do not ask what pre-research answers · what could not be asked becomes a 1-d assumption · **volume format** (the three-part `■ Abstract` at the head · table cells within three lines · no change directive numbers in the body). Appendices A-C follow §4 and §5. **Fill the abstract last, after the full text is written** — mission and intent are carried by the start confirmation right below, so do not repeat them.
7. **Dry run** — runs **before** approval: walk decision points D-n → exception responses → conditions for switching to alternatives. Check that every immediate-report condition has a decision point, every constraint has a reason, and the when-the-user-is-absent behavior (item 5) exists. **Check volume too** — are all three abstract parts filled, is any table cell over three lines. Cells that run over keep only what is currently settled, with history moved down to appendix D. When a hole turns up, reinforce the draft and walk it again — an approved order patched through a dry run invalidates its approval.
8. **Start confirmation → approval** — after the dry run, **first check whether a first-review session has been designated.** Look at two places: the user's instruction, and the controlling-session line of the parent strategy.md (see the campaign skill). If one is designated, send the draft to that session first and get plan-review passed, then go to the start confirmation; if none, record the fact that none is designated in item 4 of the approval summary. **Making the check leave a written result is the gate** — if "if designated" is only a condition, a run that skipped the check and a run with no designation cannot be told apart. Fill the `■ Start confirmation` at the head (`${CLAUDE_PLUGIN_ROOT}/templates/report-confirmation.md`: mission in my own words / intent / what is decided autonomously / what is asked), and show the user **the approval summary (format below) in the response body, together with the full text** — the full text may go in the response body, be sent as the local `opord.md` file, or be pointed to by path. Dumping tens of KB of full text into the response body buries the summary and gets in the way of the approval decision. **What is fixed is not the means but the place** — **before approval, the full text lives only on the user's screen and the local `opord.md`; it is not posted to GitHub.** Only the key block goes into the issue body (§7), and the start-confirmation comment carries **only the four items**, per the posting line at the head of `report-confirmation.md`. Post it as an issue comment through the host's notification channel (`report-posted` extension point; default: a plain issue comment) — awaiting approval. Do not cram the full text into that comment: the template pins "the post is the four items and nothing more" as a notification-sized summary, and the place where the full text stays on GitHub is the original comment after approval (§7). The scope the user may correct at approval is as in the template's usage notes. Once approved, raise the order status in the progress file to `approved` and mirror it per §7.

   > Extension point: `report-posted` fires here. See `docs/extension-points.md`.
9. **Dispatch transition** — if handing to a subagent, the brief is the work instruction, and the first report must be the start report (`${CLAUDE_PLUGIN_ROOT}/templates/report-backbrief.md`, all seven items). If executing inline, transition to implement.

   This skill expects the executing skill to honor the work instruction's reporting duties (start report, progress reports, immediate reports, completion report). They are provided by the host project's execution skills (a later stage of this plugin ships a default).

### Approval summary — response-body format

**A response asking the user for approval puts four blocks before the full text.** The full text follows, or is pointed to as a local file (§3-8). Approval requests for campaign and strategy use this format too (the campaign skill's approval request).

```
■ Approval summary
1. Decisions for approval — <the user decisions this approval finalizes, one by one; "none" if empty>
2. Mission · end state — <what is done and how far, one paragraph>
3. Scale · user time — <number of tasks and commits (for a campaign, number of DPs) · where and how many times user time is spent>
4. First review — <name of the session that passed plan-review, or "none designated">
```

**If a plan revision rides along, put it in item 1** — state which item of the parent campaign or strategy changes and how. A user who approves only the order does not know that the revision is being finalized along with it. Item 1 differs from `What is asked` (the four start-confirmation items) — that is a question not yet answered; this is an item where approval itself is the finalization. Where they overlap, leave them overlapping.

Write the four blocks yourself after reading the full text. Without them the user must read the whole text to decide. **Item 4 is the slot that records the check result** — so that a run that never looked for a designation is not mixed up with a "none designated" run, write down what you found in the two places checked (the user's instruction and the strategy.md controlling-session line). In approvals that carry a plan revision, what gets finalized together is especially hard to see.

## 4. Appendix A — Task detail rules

### Resolution — pin decisions exactly, hand off implementation

The work instruction is **a document that carries decisions, not implementations.** What the executor cannot fill in is not function bodies but which type to use, which layer to put it in, what the existing analogous implementation is, and what the edge cases are. All of that can be written without an implementation.

**Pin exactly:**

- Type and method signatures
- File paths + the `file:line` of the existing analogous implementation to follow
- Edge cases and the expected behavior in them
- A list of test case names
- The commit sequence (appendix B)

**Hand off to execution:** function bodies, boilerplate, obvious mappings, error-handling idioms.

Pseudocode only when branching or order is non-obvious, three or four lines. Pseudocode that is the implementation in another notation has the same problem as pasting the code whole, and is worse because it does not even get compiled.

**Relation to writing-plans' No Placeholders** — what that principle blocks is decision-dodging (`TBD`, `add appropriate error handling`, `write tests for the above`). That requirement stands, and "pin exactly" above satisfies it. The only line this clause overrides is `Steps that describe what to do without showing how (code blocks required for code steps)` — **the code-block obligation is waived here.** Do not read this as a conflict and decide on your own.

### Headings and structure — SDD-compatible

- Task headings are `### Task N: <title>` — the SDD task-brief script cuts on `^#+[ \t]+Task[ \t]+N`. Match the numbering to the body's 3-c `T-n`.
- Give each task **Files** (Create/Modify/Test paths), **Interfaces** (Consumes/Produces — names and types adjacent tasks use), and checkbox `- [ ] Step k` entries.
- Commit steps do not use a `feat:` message; they refer to the matching entry of appendix B.

### Decision-point gates by task type

**New settings / selection UI** — if a task builds a new settings screen or a selection/editing UI (including widget edit sheets and intent-parameter UIs), check that the four items below are decided in the spec or order. If any is undecided, stop writing and return to the §3-5 question round to settle it — all four change implementation branches:

- The list's **sort criterion and inclusion/exclusion scope** (e.g., upcoming order, whether holidays are included)
- **Localization** of labels and copy
- The **display and activation conditions** of items and parameters (e.g., show the occurrence option only for repeating events)
- Whether **the icon can be confused with an existing icon**

**Layer classification for code moves and module splits** — an order that moves types or files to another module must classify each moved item's layer (per the host's architecture, e.g., use case / service / engine) and list it explicitly. Do not classify in bulk on a surface signal such as "has an SDK import" — e.g., use cases stay in the domain module (consuming the service protocol); only service implementations move down. Find an analogous precedent for the split structure, compare against it, and write down the result.

**Minimum-new-elements check** — for every type, protocol, wrapper, or intermediate layer the order introduces, **first ask: "can this be solved by injecting the dependency directly into an existing object?"** Introduce only when there is concrete ground that direct wiring is impossible, or when there are two or more current consumers — write the ground in the task body, and if you cannot write it, do not introduce. The criterion is the host project's file conventions (adding to an existing type is the default; a new element needs grounds). Also enumerate, down to the type level, the operations and transforms the task will use, and look for existing extensions and utilities; for anything reusable, write `<operation> reuses existing <symbol> (file:line)` in the task body — if discovered late, during implementation, duplicate logic gets written first.

### Self-sufficiency check — before saving

Before saving, ask the following. If any answer is "only in this session's memory," move it into the document:

- **What the goal is and what must be done** — is it answered by the item-2 mission plus the 3-c task list?
- **What to edit and where the scope ends** — is it answered by appendix A Files plus the 3-d constraints and delegation scope?
- **By what process and where to commit** — is it answered by appendix A steps plus appendix B?
- **Premises found through exploration** (current behavior at file:line, pitfalls, known false positives) — are they written in the 1-a pre-research results or the task body?
- **The host-rule clauses the executor must follow** (e.g., the host's test-double and wait-API rules when tests are involved) — are they excerpted in the task body? Executing subagents do not get automatic loading by path matching
- **For each new type or indirection layer, is the required ground written (direct injection impossible, or two or more consumers)?** — if not, return to the minimum-new-elements check
- **Are the five "pin exactly" items of Resolution filled for every task?** — signatures, paths and analogous-implementation `file:line`, edge cases, test names, commit sequence. If any is empty, the executor guesses or comes back with a gap report

## 5. Appendices B and C

### Appendix B — commit sequence

- Plan the final commit list in advance: **logical-unit groupings + a draft of each `[#issue] behavior-change summary` message**, following the host project's commit conventions.
- **Task boundary ≠ commit boundary.** Several tasks can bundle into one commit; state that mapping in the sequence (e.g., "commit 2 = Task 2+3").
- Commits are result-oriented — do not put TDD intermediate states (RED/GREEN steps) in commits.
- Changing the sequence is reported after the fact (completion report, item 7) — it is not subject to prior approval.

### Appendix C — model tier per task

So the executing session can dispatch as written without re-judging, decide a model tier for each task and state it in a table:

- **Mechanical transcription** — decisions pinned to signature and path level, 1-2 files: **lower-tier model (haiku class)**
- **Integration and judgment** — multi-file coordination, matching existing patterns, deriving code from a prose spec: **standard model (sonnet class)**
- **Design judgment** — architecture decisions, broad codebase understanding: **top-tier model** (if such a task exists, the order is under-cooked — recheck the scope-clarity precondition)

**Do not put per-task review verdicts in this table.** This workflow does not dispatch reviewers per task — agent review is one final whole-branch pass just before the PR (the host's execution skill). There is nothing to decide.

## 6. State transitions

The order status lives on the `Order status:` line of the progress file (§8) — the body layer (opord.md) has no status field. There are six values, and who changes them differs:

| Status | Transition point | Owner |
|---|---|---|
| pre-research | intake marks start on the board — progress-file seed created | intake |
| draft | draft saved → §7 mirror | this skill |
| approved | user approves at the start confirmation → §7 mirror | this skill |
| executing | first task started | implement |
| in review | PR created · completion report (`${CLAUDE_PLUGIN_ROOT}/templates/report-debrief.md`) | pr |
| closed | PR merged — **the order is achieved only at merge** | pr (merge and cleanup stage) |

**S (a run with no work instruction) skips `draft` and `approved`** — `pre-research` (intake) → `executing` (implement) → `in review` (pr) → `closed` (pr). There is only a progress file and no issue-body mirror (there is no body layer to source a key block from) — that progress file is for status display only. The transition owners are the same as in the table, so S raises the status at each stage too. If not raised, any status display shows `pre-research` throughout implementation and review.

Every transition reassembles the issue-body mirror (§7). Changes during execution accumulate as appendix D change directives (`${CLAUDE_PLUGIN_ROOT}/templates/frago.md`) — write only the changed items, and "unchanged" for the rest. **The review stage is the same** — before merge, review can discard the work or change direction and plan, and if a review fix changes the body layer (scope, structure, deliverables), accumulate it in appendix D as a change directive.

**Issuing a change directive is one bundle of three** (missing any is a deviation): ① accumulate in appendix D + record its full text as an issue comment (the posting line at the head of `frago.md` — history layer) ② reflect the change in the body layer (`opord.md`) — **delete the old wording at the changed spot. Appendix D owns the history; the body states only what is currently settled** — rewrite the head abstract's `This change directive` and `Current state and what remains`, then update the progress file's `Key:` line (§8) to the latest settled state and reassemble the issue-body mirror (§7) — the issue body's key block is always the **final state** with changes against the original reflected. Reassemble even when the change does not touch the key-block items (mission, end state, scope, task list) — the `Key:` line and the change-directive links change ③ progress-updated declaration.

> Extension point: `progress-updated` fires here. See `docs/extension-points.md`.

An accumulation of change directives and immediate reports is a **deviation signal that the run diverged from the initial plan** — a host status display may tally it, and frequent occurrence is reported to the user as a sign of weak planning (the implement plan-gap loop).

**Encroachment on an adjacent DP's owned scope** — the path that detects it is the delegation decision-authority table's `edit file outside scope = prior approval`. At the point where you stopped because you must touch a file outside your own scope (appendix A Files + 3-d constraints), **check whether that file falls within another DP's `owned scope` cell in campaign.md item 9** — if so, it is encroachment and the following applies. For a standalone M with no item 9, check against what 1-e adjacent work records, plus open PRs and worktrees. Skipping the check gets only the out-of-scope approval, and the other side never learns its scope was cut. For encroachment, the encroaching side leaves a notice. ① Post to the other DP's issue a comment in the `${CLAUDE_PLUGIN_ROOT}/templates/report-immediate.md` format, stating the encroached scope and reason, through the host's notification channel (`report-posted` extension point; default: a plain issue comment), and stop until approved — the other side's `opord.md` is in its session's worktree and gitignored, so this session cannot write it; the only layer both sides share is the issue.

> Extension point: `report-posted` fires here. See `docs/extension-points.md`.

**When getting approval, also ask that the other session be told** — that session is executing and has no clause to re-read its own issue comments, so the user is the only delivery path. **Once approval comes through, the encroaching side also leaves a change directive in its own appendix D** — appendix A Files and the 3-d constraints widened, so both sides' orders must change together. If the side that lost the scope is not fixed, the next session entering on that order alone reads the file as its own scope. ② The notified session absorbs the content into its own appendix D as a change directive (reason `adjacent-DP encroachment notice received`, same three-part bundle as above) — with notice but no absorption, the other order keeps presuming the pre-encroachment owned scope. **If there is no session to receive it, the notice comment is the end** — if the adjacent DP is still `not started`, it has no opord and no appendix D, and its intake reads the issue comments and picks it up. If already `merged`, there is no order left to fix. ③ The primary list of notice targets is the work instruction's 1-e adjacent work and 3-d adjacent-work notification. **If you newly discover a DP during execution that is not on that list, first update 1-e by change directive before notifying** — surfacing an adjacent relation missed at planning time is this procedure's main trigger, so a static list leaves the target empty in exactly the case that needs it.

**Rewrite boundary** — if the intent (3-a) changes, it is a rewrite, not a change directive. **If a user instruction intends a plan change** (a case already beyond what a change directive can cover), a run with a parent plan (a DP of an L) gives priority to that change or partial revision (the campaign skill's §4, plan revisions), then prompts the order rewrite — not fix the order first and trail the plan to match. A standalone M with no parent plan goes straight to an order rewrite.

## 7. Storage and mirror

- Path: `<operations directory>/<issue number>/opord.md`. For a DP, `opord-<DP>.md`. Gitignored — other sessions and worktrees restore it by **applying the approval original comment + the appendix D change-directive comments in order** (or from a status-display spool, if the host keeps one). The issue body carries only the key block, so the full text cannot be restored from it — do not enter execution reading the body alone.
- Separate the body layer from the progress layer — the **body layer** (sections 1-5, appendices A-D) is updated at draft finalization and when a change directive (appendix D) is reflected. **Progress** (order status, task progress) is updated freely in the progress file (§8). Neither is committed to git (the canonical sharing of the body layer is the approval original comment; the progress layer is the issue-body mirror, and committing would add worktree/branch-sync cost; status-only commits are also prohibited).
- **Issue body = the order's key block + the `<!-- progress -->` block.** Do not carry the body-layer full text — the full text lives in the approval original comment. Carrying the same full text in both the body and a comment bloats the issue and forces readers to work out which is latest. The key-block format is fixed:

  ```markdown
  ## Work instruction — #<issue> <name>
  Parent: <campaign #n / LOE-x / phase y / DP-x.y / predecessor DP; for M, "standalone">
  Key: <the progress file's `Key:` line (§8) verbatim — the latest gist with changes against the original reflected>
  Mission: <the item-2 mission, one sentence>
  End state: <the 3-a end state — one line each, only the applicable ones of behavior, code, structure, verification, external>
  Scope: touch <union of appendix A Files> / do not touch <3-d constraints>
  Tasks: <3-c T-n, one line each — appendix A-C detail is not carried>
  Original: <link to the approval original comment — "awaiting approval" before approval> · Change directives: <links to FRAGO-n comments …>
  ```

  The question the body answers is "what work is this issue, and how far along is it now," not "how is it built" — that is why the execution instructions do not come into the body. Reassemble after the draft is saved, after approval, and at each task completion, change-directive reflection, and §6 state-transition update — task-start marks and the activity log feed only the progress-updated declaration and are not mirror-reassembly targets (§8, implement). Reassemble by joining the key block and progress.md and writing that as the issue body, following the host's issue conventions (a later stage of this plugin ships a default). There is no separate summary comment, and no marker comment — the body itself is the latest settled state. Report comments (those marked as posted in their posting line — start confirmation, completion report, and so on) are not covered by this prohibition — they are the history layer.

  This skill expects the host's issue conventions as input for the issue-body mirror duty and the comment protocol. They are provided by the host project's issue skill (a later stage of this plugin ships a default).
- **Right after approval, post the confirmed original full text as an issue comment through the host's notification channel (`report-posted` extension point; default: a plain issue comment), then put that comment's URL in the key block's `Original:` line and reassemble the mirror** (if the order is reversed, the body goes up with the link slot empty) — since the body carries only the key block, **this comment is the only place the full text stays on GitHub.** It is a snapshot at approval time and is not edited afterward — changes accumulate in the appendix D change-directive comments, and the two applied in sequence are the latest full text. Without this comment, the full text exists nowhere outside the local worktree. It is a history-layer post like the start confirmation and completion report, so it is not covered by the marker-comment prohibition above.

  > Extension point: `report-posted` fires here. See `docs/extension-points.md`.
- Progress syncing is more frequent than the mirror — **every time the progress file or activity log (§8) is written**, not only at mirror reassembly, the progress state has changed. A host's status display may sync on it; a failure there must not block.

  > Extension point: `progress-updated` fires here. See `docs/extension-points.md`.

## 8. Progress file ↔ SDD ledger

Both are used — they are different layers:

- **Progress file `<operations directory>/<issue number>/progress.md`** = the canonical progress record of the planning layer (replaces the old appendix E). Format: a `<!-- progress -->` heading + an `Order status:` line + **a `Key:` line** (the gist of the order in one or two sentences **on a single line, no line breaks** — a status-display parser may read only the first line, so everything from the second line is silently cut. At the intake seed it holds the issue title, and **it is rewritten to the order's gist at draft-save time** — the mirror is already reassembled then (§7) and exposed in the body, so deferring to approval would put up a body whose mission and end state are draft but whose `Key:` line is still the issue title. Rewrite it again when the user corrects at approval, and afterward whenever a change directive alters mission or scope, keeping it the latest settled state. The head of the issue body and any status display read this line) + the task table `| Task | Status | Commit | Report |` (the basis for progress reports — filled at draft-save time, when appendix A is finalized; absent at the seed stage). It is gitignored but remains on GitHub through the issue-body mirror — other sessions and worktrees restore it from the issue body. **The one who creates the file is intake** (status `pre-research`) — this skill takes over that seed and raises it to `draft`. If invoked directly without intake and there is no seed, this skill creates it.
- **Activity log `<operations directory>/<issue number>/activity.md`** = the live feed of the execution layer — a status display's drill-down may read it. For each meaningful activity — task start, GREEN, commit, report, blocker — append one line `- MM-DD HH:MM content` (updated by implement). **Review rounds after the PR goes public are covered too** — one line each for review received, fix pushed, thread reply, and switch to awaiting re-review. The execution layer stays alive during `in review` as well (the close is the merge — §6) — if the log breaks here, the display shows no activity after the last task (updated by the skill carrying out the fix — implement, review-direction gate). Gitignored and not carried in the issue mirror — the mirror is the settled state, the activity log is the flow.
- **SDD `.superpowers/sdd/<plan-basename>/progress.md`** = the superpowers plugin's execution-layer ledger. For ruling, recovery, and resumption — the plugin's own domain, so leave it as is.

Update the progress file at task state transitions (including marking `executing` at start), completion, and commit, plus the §6 state transitions; update the ledger per the SDD procedure.

> Extension point: `progress-updated` fires here. See `docs/extension-points.md`.

## 9. End record — skill-end

When the work instruction is saved and approved and the procedure ends, the skill has finished its work.

> Extension point: `skill-end` fires here. See `docs/extension-points.md`.

If superpowers:writing-plans was actually invoked in this run, the same moment ends it too — the plugin skill has no end clause of its own, so this is where it is closed out. **superpowers:brainstorming is closed out by the same rule** — its end is the moment divergence finishes and the direction is settled as a work instruction. A run that goes straight to implementation without a work instruction leaves the end moment to the implement skill.
