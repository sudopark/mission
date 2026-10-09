# Release Plan — XL

> Terms — DP: unit of work (one work instruction = one PR) · LOE: work line (owns one aspect of the end state) · FRAGO: change directive · MOP / MOE: measure of performance (was the task done) / measure of effect (did the effect occur) · PIR / FFIR: immediate-report conditions from environment/external information / from our side/internal information · ends / ways / means: purpose / campaigns / resources

```
Release plan — #<parent issue> <name>        Author: user   Revision: no. N (date) — one sentence
                                      — The header carries only the latest revision line. Earlier revisions live in item 13 (history)

■ Abstract — four parts, one or two sentences each, ten lines total. The current state must be fully readable here without reading the rest
   What and how far — what this strategy does and its scope boundary (summary of item 1)
   Current stage and what remains — which campaign is running and what is unfinished. **Edit only when a campaign starts or closes, and on revision** (the source of truth for campaign-level status is the item 12 ledger)
   This revision — what the previous revision changed ("draft" if this is the first draft)
   User decisions pending — items awaiting approval or a choice ("none" if empty)

1. Purpose (ends) — what is different once this bundle is done
2. End state (completed form + how to judge it)
3. Closing and stopping criteria — successful close (= item 2 reached) / stop or scale-down conditions / cleanup on stop (handling of closed and in-progress campaigns)
4. Means and constraints (means) — source of applicable rules / dos and don'ts specific to this bundle (with reasons) / ends-ways-means-risk consistency — if the means fall short of the purpose, state either a reduced purpose or an accepted risk
5. Delegation ceiling — based on the decision authority table + what widens or narrows it
   Controlling session — if one session controls the campaigns, its name, the gates it holds (plan-review before approval · PR review before merge · report check before output · wording check before PR creation), and standing instructions to subordinate sessions. Write the name, never the socket address (campaign skill §3)
6. Campaign list (ways) | Campaign | Issue # | Purpose | Predecessor | Ordering rationale | — the campaign cell starts with an id like `C1 <name>`, and the predecessor cell uses that id (`none` if there is none)
7. Campaign arrangement — serial / parallel / conditional start · main-effort campaign (resources first) · order for early validation of assumptions
   Flowchart — the campaign arrangement as a mermaid flowchart: campaign = node, predecessor = solid edge, conditional start = dashed edge labeled with the decision point (same derivation rule as campaign.md item 8)
8. Strategy assumptions | ID | Assumption | How to validate | If broken (decision point ID — re-review if none) |
9. Strategy risks | Risk | Affected campaigns | Accept / mitigate / avoid | Rationale |
   Handling limit — the point where "past here, stop starting new work" (concurrent campaigns, user approval throughput, etc.) + the indicator
10. Strategy decision points | ID | Information for judgment | Decision (rearrange, cancel, or re-evaluate campaigns) | Default if undecided |
11. Evaluation — sum of campaign MOEs → judgment of whether the purpose is achieved / re-evaluation triggers (a campaign closes + a strategy assumption collapses or the external environment changes)
12. Campaign ledger → <operations dir>/<issue>/strategy-progress.md — ledger table | Campaign | Status: not started / in progress / closed | Latest completion report | Notes | — the campaign cell starts with the same `C<n>` id as item 6 (not committed; mirrored into the `<!-- progress -->` block of the issue body)
13. History | Revision | Date | Items changed | What changed and how | Why |   — all revision history goes here. Body cells state only what is currently settled
```

- **Keep each table cell within three lines (about 200 characters).** If it runs over, it is carrying several decisions or revision history — keep only what is currently settled and move the rest to item 13
- **Do not cite revision numbers in the body.** The body states only what is settled now; item 13 alone records what changed, when, and why
- Item 6 is the definition (edit this file only when the plan changes); item 12 is state — update it freely in the progress file at every campaign start and close. Issue body = this file in full + the `<!-- progress -->` block
