---
name: control-brief
description: Use when the controlling session must (re)announce the reporting gates to its subordinate sessions — rebuilding the reporting chain, a session skipped a gate, or the same directive goes to several subordinate sessions at once. Triggers on "tell the subordinate sessions the reporting chain again", "propagate this to the sessions", "send this directive to the subordinate sessions". Does NOT trigger on plan-document review (plan-review skill), PR review, vetting a user report (report-review skill), or a single session answering a message from a peer.
---

# Control Brief — Conveying the Reporting Chain to Subordinate Sessions

> **Runtime requirement.** This skill needs an environment with session-to-session messaging: a way to look up another session's address (the session directory) and one reserved name for the controlling session. See contract 4, "Runtime environment", in `docs/extension-points.md`. Everything below refers to these abstractly: "the session directory" for the lookup, "the reserved name" for the name the messaging layer treats as the sender itself. Which tool provides them, and which name is reserved, is the host's and the runtime's to say. A host without this capability can still use every other skill in the plugin.

**The control gates are only a promise; nothing mechanical enforces them.** If a subordinate session does not stop by, the gate is simply passed. This skill announces the gates again and, when needed, attaches directives to the same message. One incident of each kind: a PR merged with no review, and a campaign-plan revision approved with no plan-review.

## 1. Securing the targets

- Look up the live peers in the session directory. If the user names targets, send only to those sessions; if not, send to every subordinate session of this host project.
- Leave out sessions that belong to other repositories. If the name alone does not settle it, check whether the working path the session directory reports is inside this host project or its worktrees.

## 2. Reply address — the controlling session's name

A subordinate session finds the **controlling session's name** in the session directory and sends to it. Do not put a socket address in the message — it changes when the session is restarted. Use the name written in the controlling-session slot of strategy.md (for a standalone campaign outside a strategy, campaign.md item 13 — campaign §3), and write the same name in the message.

- If the controlling session's name is the reserved name, change it first — the messaging layer reads the reserved name as the sender itself, so a report a subordinate session sends to it loops back to the sender and is lost.
- When you change the name, fix the controlling-session slot in strategy.md at the same time and announce again with this skill.

## 3. Composing the message

The first line is a one-sentence gist. The user on the receiving end sees only the first line as a preview. The body carries the fixed block below as it is; directives, if any, follow it.

```
[Control gates, re-announced] Before plan approval, before PR creation and merge, and before any report is shown to the user, always pass through the controlling session.

1. plan-review before approval — drafts and revisions of strategy, campaign, and opord go to the controlling session for first review before the user's approval. Only a pass goes on to approval. The authoring session obtains the approval from the user directly.
2. PR review before merge — once the PR is up, request review from the controlling session before merging. Merge only on a pass.
3. Report review before output — a message that stops and hands over to the user (completion report, decision request, analysis result) goes to the controlling session as a draft under a `[Report review]` header before it is shown. Show it to the user only on a pass. A one-line progress note and an immediate answer to a user question are not covered. The criteria, and the attachments to put in the request, are in `${CLAUDE_PLUGIN_ROOT}/skills/report-review/SKILL.md` §1 and §2 — read it before writing.
   Commit messages and PR bodies are the same — before creating the PR, send all of the branch's commit messages and the draft body under a `[PR copy review]` header, and raise the PR only on a pass (attachments and criteria are in §1 and §2-1 of the same file).

Outside these three gates (task progress, FRAGOs, running commits), each session decides.
Send reports and requests to the controlling session `<name>` — look that name up in the session directory. The name is also written in the controlling-session slot of strategy.md, so look there after a `/clear`. If sending fails, do not pass over it silently; tell the user.
<if a gate was skipped: that session's case and a one-line handling>

[Directive]
<the directive the user gave — if it differs per session, only that session's part>
```

- **Carry only the directives the user gave.** Do not append your own guesses.
- **If it is a standing directive, also write it in the controlling-session slot of strategy.md (campaign.md item 13 for a standalone campaign).** A directive meant to hold from now on, not once, is lost when the receiving session runs `/clear` if it was conveyed only in the message. A rule that belongs in the host's own harness is canonically recorded as a clause there.
- If the directive differs per session, build a separate message per session. Mixing several sessions' parts in one message makes the receiver carry out someone else's directive.
- This message grants no authority. Do not include wording that stands in for approving the receiving session's permission prompts or changing its settings.

## 4. Sending and results

- Call the messaging tool for each target in parallel.
- Tell the user about targets that failed and targets held or refused by a cross-session delivery notice. Success only means it arrived, not that it was read. Do not read silence as agreement.

## 5. End record

When the sending is done:

> Extension point: `skill-end` fires here. See `docs/extension-points.md`.
