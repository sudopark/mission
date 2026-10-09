# Work Instruction — M · DP

> Terms — DP: unit of work (one work instruction = one PR) · LOE: work line (owns one aspect of the end state) · FRAGO: change directive · MOP / MOE: measure of performance (was the task done) / measure of effect (did the effect occur) · PIR / FFIR: immediate-report conditions from environment/external information / from our side/internal information

```
Work instruction — #<issue> <name>       Draft: agent   Approval: user   Date:
Parent: campaign.md #<n> / LOE-<x> / phase <y> / DP-<x.y> / predecessor DP     (for M: "standalone")

■ Abstract — three parts, one sentence each. Mission and intent are carried by the start confirmation right below, so do not repeat them here
   Current state and what remains — which task it has reached and what is unfinished ("draft, before start" if this is a draft)
   This change directive — what the previous FRAGO changed ("none" if there is none)
   User decisions pending — items awaiting approval or a choice ("none" if empty)

■ Start confirmation — mission / intent / what is decided autonomously / what is asked

1. Situation
   a. Pre-research results — only what affects the plan, with file:line (reuse the pre-research output)
   b. Obstacles and friction — most likely form / most dangerous form
   c. Parent quotes — relevant aspects of the end state / intermediate goal of the work line / adjacent DP relations and interface contracts
       (M: purpose, problem definition, end state, assumptions, delegation, and constraints, one line each)
   d. Assumptions | ID | Assumption | Source: inherited / new / substitute for an unasked question | If broken |
   e. Adjacent work — concurrent PRs, worktrees, and planned base changes whose owned scope overlaps (record this for M too — "none" if there is none)
2. Mission — This work will <verb of the task + object> until <condition>, so that <intermediate goal of the work line / parent purpose>.
3. Execution
   a. Intent — purpose / key things to do (conditions to satisfy, not methods) / end state (behavior, code, structure, verification, external)
   b. Concept — the core change (one) / preparatory work / alternatives + conditions for switching / phases (state conditions)
       Flowchart — only when branching really exists (alternatives, contingencies, decision-point chains, non-linear task dependencies): a mermaid flowchart, same derivation rule as campaign.md item 8. Do not draw one for a linear flow
   c. Tasks — T-n: <verb of the task + object>, so that <purpose>. (details in appendix A)
   d. Conditions of execution (delete any item that has no reason)
       Start conditions / interface contracts (inherited + added) / constraints (with reasons) / delegation scope (only what narrows it) / accepted risks
       Adjacent-work notification — the DPs and issues to notify on encroachment (from what 1-e recorded, "none" if there is none)
       Buffer — the executor's room for discretion = autonomy level + the exception responses below
       Immediate-report conditions  PIR-n (environment/external) → decision point D-n / FFIR-n (our side/internal) → decision point D-n
       Decision points | ID | Decision | Information for judgment | Deadline (condition) | Default action if undecided |
       Exception responses | Condition | Action | Decision maker | Escalation |
4. Verification and resources — test targets and verification ladder / snapshot and on-device checks / model tier, parallel slots, worktrees / external resources
5. Reporting — immediate (conditions, contingencies, assumption collapse, harness gaps) / regular (phase transitions, task completion) / when the user is absent (within intent: continue on the default; if <condition>: stop) / closing conditions

Appendix A. Task details — `### Task N: <title>` headings (task-brief compatible) · Files · Interfaces · checkbox Steps / signatures, paths, analogous file:line, edge cases, test case names
Appendix B. Commit sequence (changes are reported after the fact)
Appendix C. Model tier table
Appendix D. Accumulated change directives
No appendix E — progress (order status, the `Key:` line, per-task status, commit shas, reports) lives in <operations dir>/<issue>/progress.md, mirrored into the `<!-- progress -->` block of the issue body
```

- **Keep each table cell within three lines (about 200 characters).** If it runs over, it is carrying several decisions or change history — keep only what is currently settled and move the history to appendix D
- **Do not cite change directive numbers in the body.** The body states only what is settled now; appendix D alone records what changed and why — the reading load does not grow as changes accumulate
- This full text goes in the approval comment (the original) — only the key blocks go into the issue body (opord skill §7)
- Writing order: derive tasks (stated, implied, essential) → 2 mission → 3-a → 3-b → 3-c → 3-d → 1 → 4 and 5. 3-a ≥ 3-c (if the tasks run longer, you are dictating the method)
- Do not ask what pre-research can answer. What could not be asked goes into 1-d as a "substitute for an unasked question" assumption, and the draft goes out
- At approval, the user corrects only what diverges from intent. Specifying a method is allowed in only three cases: sub-work synchronization / no discretion because of the host project's rules and norms / order of shared resources
