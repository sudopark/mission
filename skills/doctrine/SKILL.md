---
name: doctrine
description: Use when a decision has no rule or precedent to stand on — at a point in implementation or planning where it is unclear what concretely to do and no precedent can be found, ask the user to create or reinforce an execution rule (doctrine) and settle the answer as a rule. Triggers on the host execution skill's gap-report loop having reported and stopped, on opord or campaign implied-task derivation finding no governing doctrine, and on user directives like "pin this down as a rule". Does NOT trigger on a clause that exists but was not found (confirmation step 2 filters it out), a one-off judgment that will not recur, maintenance of skill or harness md files, or cases where cause and remedy are already settled and only application remains (the execution skill).
---

# Doctrine — Requiring a Rule

**If the planning skills are the harness of the planning phase, the host's rules directory (default `.claude/rules/`) is the doctrine consulted during execution.** When no doctrine exists and a decision has nowhere to stand, this skill does not fill the hole with a guess. It **requires the user to build the doctrine and settles the answer as a rule.**

The host's execution skill has a gap-report loop that reports a rules gap and stops. The user's answer lets implementation continue, but nothing turns that answer into a rule. This skill is that endpoint.

This skill expects the gap-report loop as its entry signal. It is provided by the host project's execution skill (a later stage of this plugin ships a default). The immediate-report template is `${CLAUDE_PLUGIN_ROOT}/templates/report-immediate.md`.

## 1. Entry

All three paths run the same procedure (§2–§5).

- **Gap during execution** — the point where the host execution skill's gap-report loop reported and stopped. Continue implementation immediately on the user's answer; doctrine runs §2–§5 alongside it. Landing the rule is not a precondition for resuming implementation (§5 sets the apply timing).
- **Planning-phase doctrine check** — opord §3-4 / campaign §2-1 **implied-task derivation** (not stated, but pulled in by the host's rules and architecture) finds no governing doctrine. **Do not stop the plan** — carry the request in the planning skill's question round, and if it is not settled there, pin it as an assumption and issue the draft (opord 1-d). If the doctrine is so thin that not one implied task can be derived, report that first — the plan would spin in place.
- **Direct user invocation** — a directive like "pin this down as a rule". Confirmation (§2) still applies in full.

## 2. Gap confirmation — the gate before requesting

**Distinguish "I cannot find a precedent" from "I did not look."** It is a gap only when all four searches come up empty. If any one hits, it is not a gap but something not found — follow what was found.

1. **The full text of the rules directory** — path-matched auto-loading does not fire when paths do not match. Do not look only at what was loaded; grep every file
2. **The CLAUDE.md hierarchy** — the root plus the child CLAUDE.md files along the relevant path
3. **The canonical docs** — the host's doctrine search paths, declared in the host's `CLAUDE.md` (spec, style, and domain-context documents)
4. **Sibling components** — how siblings solved the same problem. Precedent lives in code, not only in documents

```bash
grep -rn "<decision keyword>" <rules directory> <doctrine search paths>
grep -rn --include=CLAUDE.md "<decision keyword>" .
```

Search CLAUDE.md files separately with `--include` — child files live at any depth. A 1-depth glob like `*/CLAUDE.md` catches only the two directly under the root and silently drops the rest.

This command covers 1–3 only. **Search 4 separately** — code precedent uses a different vocabulary from documents and is not caught by the same keywords. Grep by naming sibling type and protocol names; if the surface is so large that you do not know where to look, delegate to a code-analysis agent. Never confirm "absent" with the fourth search skipped.

Once confirmed, classify the kind of gap — the remedy differs.

| Kind | Signal | Remedy |
|---|---|---|
| **Absent** | no clause, no precedent | create a rule, or add a section to an existing rule |
| **Conflict** | two clauses give different answers | a reinforcement that states the priority |
| **Ambiguous** | a clause exists but two implementations both satisfy it | a reinforcement that sets the deciding criterion |
| **Precedent split** | siblings did it differently from each other | a creation or reinforcement that names the canonical one |

## 3. Recurrence gate — qualifying to become a rule

**Will this decision come again?** A judgment used once and discarded is not a rule — decide it on the spot and be done. The basis must be observation, not imagination: several components of the same type already exist; the decision touches a recurring work type (a new screen, a new repository, a new widget kind, and the like); or the decision creates two paired locations.

With no recurrence basis, **do not make the request.** A rule is leverage that multiplies across all later work; raising a one-off judgment to doctrine hands the next person a constraint with no basis (YAGNI).

## 4. The request — what to put before the user

**Do not fill with a guess, but bring the materials so the user only has to choose.** Include five things:

1. **The blocked decision** — what must be decided, in one line
2. **Confirmation evidence** — where §2 looked and what was missing (paths, grep results), plus the kind of gap
3. **Viable options and their reach** — how many sibling components and which paths each option touches
4. **A default** — recommend one and give the basis. If the user gives a different answer, that answer is canonical
5. **Apply timing** — separate commit now / separate cycle after the current work / accumulate as an issue (§5). Collect it here — do not ask again after writing the rule text. The host may preset a default in its `CLAUDE.md` to skip the question

**This is the only place to ask.** For planning-phase entry, hand these five to the planning skill's question round as items — do not open a separate question round. The rule content and the apply timing are independent, so they can be asked in one go; asking them separately stops planning or implementation twice.

## 5. Apply — destination and timing

**The nature of the decision sets the destination.** Where it is written is when it is loaded; get it wrong and the clause does not appear where it is needed.

| Nature of the decision | Destination |
|---|---|
| How code is written — authoring and implementation rules | the rules directory — reinforce an existing file if its `paths:` covers that path, create one if not |
| What exists here and how it connects — a module's structure, concepts, domain knowledge | that module's child CLAUDE.md |
| Always applies regardless of path — absolute rules, paired locations | the matching section of the root CLAUDE.md |

**Splitting by scope does not work.** A rules file's `paths:` and a module's child CLAUDE.md can cover the same module, and overlapping scope is normal (e.g. the host's domain rules file and the domain module's child CLAUDE.md). What separates them is the nature in the table above.

**If one side does not exist at all, the other doubles for it** — a framework with no dedicated rules file has its child CLAUDE.md carry the structure and the authoring rules together. Before going to creation, check whether the path has a child CLAUDE.md — if it does, reinforcing it is the default. Putting one authoring rule into a broad rules file whose `paths:` covers many modules loads that clause on every unrelated task.

A decision that always applies, placed in the rules directory, stops being always-loaded the moment a `paths` is attached — narrowing the scope makes the clause go silent.

- **Reinforce** — add the clause to the relevant section. If it conflicts with an existing clause, rewrite that clause (do not append — two coexisting answers create one more gap)
- **Create** — a new file in the rules directory. Frontmatter `description` (a one-line gist) and `paths` (globs of the auto-load targets) are required. Without `paths` the file exists and is never loaded

Write the clause as a **decidable sentence**. "Handle appropriately" is not doctrine — what triggers which side must give any reader the same conclusion. Attach the why in one line: a constraint with no basis gets routed around by the next person.

**Apply timing — follow what the user chose.** The three options were already collected in §4-5: separate commit now / separate cycle after the current work / accumulate as an issue. It depends on the work's circumstances (size of the original work, PR boundary, size of the rule change), so the session does not decide for itself, and does not commit on its own just because the rule md is written.

If the user chooses anything but applying now, **leave a record on the spot** — the §2 confirmation evidence, the §4 options, and the user's chosen answer, as a comment on the original work's issue or as a follow-up issue (same form as the execution skill's plan-gap loop). Do not use the host's harness-improvement backlog, if it has one — its intake is typically reserved for usage-triage signals, and mixing in a different kind of item pollutes triage. Only deferring with nothing written anywhere is forbidden. The invariant is not losing it.

## 6. Red Flags

- The user's on-the-spot answer is **merely an application of an existing clause**, yet it is raised to a new rule — reread the clause
- Polishing rule text while the original work sits stopped — request and apply stay short; return to implementation

## 7. End record — skill-end

Once per gap. A run that filtered at §2 or §3 and ended without making a request is also an end — filtering is this skill's fulfillment.

> Extension point: `skill-end` fires here. See `docs/extension-points.md`.
