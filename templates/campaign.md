# Campaign Plan — L

> Terms — DP: unit of work (one work instruction = one PR) · LOE: work line (owns one aspect of the end state) · FRAGO: change directive · MOP / MOE: measure of performance (was the task done) / measure of effect (did the effect occur) · PIR / FFIR: immediate-report conditions from environment/external information / from our side/internal information · branch / sequel: alternative path within a phase triggered at a decision point / follow-on path by phase end state

```
Campaign plan — #<parent issue> <name>        Author: user   Revision: no. N (date) — one sentence
                                        — The header carries only the latest revision line. Earlier revisions live in item 19 (history)

■ Abstract — four parts, one or two sentences each, ten lines total. The current state must be fully readable here without reading the rest
   What and how far — what this campaign does and its scope boundary (summary of item 2)
   Current phase and what remains — which phase this is and what is unfinished. **Edit only on phase transitions and revisions** (the source of truth for DP-level status is the item 18 ledger — making this follow it would add one more thing to keep in sync)
   This revision — what the previous revision changed ("draft" if this is the first draft)
   User decisions pending — items awaiting approval or a choice ("none" if empty)

0. Release plan — purpose (parent issue # or a quote from strategy.md) / source of means and constraints / delegation ceiling
1. Situation — pre-research summary (only what affects the plan, with file:line — reuse the pre-research output) / obstacles and friction — most likely form / most dangerous form
2. Problem definition — current state / desired state / what blocks it / out of scope
3. Closing and stopping criteria — successful close (= item 4 reached) / stop or scale-down conditions (what breaking forces a review of the campaign itself) / cleanup on stop (handling of merged DPs and open branches)
4. End state (completed form + how to judge it) | Aspect: behavior / code / structure / verification / quality / external | State | Judgment |
5. Core obstacle analysis — source of strength / core obstacle + point of attack / approach: direct, indirect, or erosion — why (including the rationale for choosing among COAs)
6. Work line LOE-n <name>: aspect owned → intermediate goal ① → ② → ③
7. Phases (conditions expressed as states) | Phase | Entry condition | Exit condition | Main-effort LOE | Purpose |
   A phase name must show what the phase accomplishes. Examples: pre-validation (exhaust the riskiest assumptions) · groundwork (build footing and foundations) · core build (one thread through every layer) · expansion (replicate and extend the fixed contract) · wrap-up (clean up leftovers, guard against regressions). This is not a fixed list — name the campaign's own phases at this level of vocabulary. Naming a phase by the nature of the work ("infrastructure", "pilot") erases from the name why the phase sits in this order and how it differs from the next — the nature of the work belongs in the purpose column
   sequel — follow-on by exit state (met → next phase / partial or unmet → <path>) — only for phases that fork
8. Work order — work line × phase grid → DP coordinates
   Sequence — parallel bundles and serial chains + rationale (predecessor dependency / early validation of assumptions / handling limit) — mapped to parallel slots
   Inter-task agreements — interface contracts and merge order between adjacent DPs (friction points only)
   Flowchart — grid, sequence, and branches as a mermaid flowchart: phase = subgraph, DP = node (LOE noted), predecessor = solid edge, branch = dashed edge labeled with the decision point. The tables are the source of truth — the flowchart is derived and regenerated whenever the plan is revised
9. DP list | DP | LOE | Phase | Content | Predecessor DP | Owned scope (modules/files) | Size S/M |
10. Decision points and branches | ID | Coordinate (`LOE-n · <phase>` — `standing` if it is a standing condition) | Information for judgment PIR/FFIR | Decision | Deadline (condition) | Default if undecided |
    branch | Triggering decision point | Content (up to DP rearrangement, substitution, or scope reduction — task-level contingencies belong to the work instruction) | Decision maker |
11. Assumptions | ID | Assumption | How to validate (which DP or check confirms it early) | Validation result (unverified / confirmed / broken + evidence) | If broken (decision point or branch ID — escalate to re-review if none) |
12. Risks | Risk | Affected LOE | Accept / mitigate / avoid (branch ID allowed) | Rationale |
    Handling limit — the point where "past here, stop starting new work and digest what is in flight" + the indicator (PRs awaiting review, approval throughput, branch divergence, etc.)
13. Resources | Phase | Main-effort resources | Supporting resources |   — parallel slots, worktrees, external accounts, user time
    Controlling session — only for a campaign controlled on its own, without a strategy: the controlling session's name, gates, and standing instructions (for a campaign under a strategy, strategy.md records them; campaign skill §3)
14. Evaluation — MOP (judging DP completion) / MOE | Aspect | Indicator | Source of judgment data (tests, snapshots, on-device runs, usage metrics) | / evaluation timing (completion report, phase transition)
15. Delegation — inherits delegation.md; record only what narrows it
16. Standing constraints — do (reason) / don't (reason)
17. Standing immediate-report conditions — every condition needs a matching decision point (item 10 ID). Default entries: a limit or delegation judgment splits and no rule clause covers it → harness gap / a merged DP's output is reverted on that DP's base branch → move that DP to `regressed` and re-judge whether follow-on DPs are eligible to start
18. Ledger → <operations dir>/<parent issue>/campaign-progress.md (not committed; mirrored into the `<!-- progress -->` block of the issue body)
    DP table | DP | Status: not started / started / executing / in review / merged / regressed / dropped | Issue # | Branch | Base | PR # | Notes | + current-phase line + waiting line (`Waiting: <what> — <on whom>`, `Waiting: none` if empty)
    `regressed` means the merged output was reverted and the DP must be redone; it is in progress, not closed. `dropped` is a closed state: a requirements redefinition means the DP will not be done
    Decision table | Decision point | Status: not reached / reached, undecided / decided | Triggered branch | When | Notes |   — only decision points that have been reached or triggered
19. History | Revision | Date | Items changed | What changed and how | Why |   — all revision history goes here. Body cells state only what is currently settled
```

- **Keep each table cell within three lines (about 200 characters).** If it runs over, the cell is carrying several decisions or revision history — keep only what is currently settled and move the rest to item 19. Judge by line count, not by eye
- **Do not cite revision numbers in the body** (as in "since revision 15" or "after the 11th reselection"). The body states only what is settled now; item 19 alone records what changed, when, and why. If understanding the current decision needs a reason, write the reason without the revision number
- Item 9 is the definition (edit this file only when the plan changes); item 18 is state — update it freely in the progress file at every start, PR creation, completion report, and merge. Issue body = this file in full + the `<!-- progress -->` block
- The coordinates and validation results in items 10 and 11 determine where decision-point nodes sit on the status board grid and the color of assumption chips — leave a coordinate empty and that decision point drops to a line outside the grid
- Split a DP until it is "one PR, finished within days". If it will not split, that DP is a subordinate campaign
- Size M does not produce this document — put items 0, 2, 4, 11, 15, 16, 17 in work instruction 1-c, one line each
