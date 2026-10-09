# mission

A plan-and-order harness for Claude Code: work instructions, campaign plans, release plans, and their reporting contracts, packaged as a plugin.

The harness splits work by size. A single-PR issue gets a work instruction. Multi-PR work gets a campaign plan, and the largest efforts get a release plan above that. Every level states its intent and end state, delegates execution within that intent, and reports back at defined moments. The plugin is general-purpose: it carries no project-specific paths, scripts, or board wiring. The host project supplies those through a small set of contracts.

## Origin

The system derives from military command practice, specifically chain of command and mission command. In a chain of command, each level issues orders to the level below and answers to the level above. Mission command adds three ideas: the commander states the intent and the desired end state, subordinates execute with freedom inside that intent, and they follow a reporting discipline so the commander learns of deviations early.

Here the commander is the user and the controlling session, and the executors are subordinate sessions. The plans carry the intent and end state, the delegation rules say what an executor may decide alone, and the reports are the points where an executor must check in.

Names such as `opord` (operation order) and `frago` (fragmentary order, here a change directive) keep that origin. All body text in the skills and templates uses plain terms instead.

## Skills

| Skill | What it does |
| --- | --- |
| `opord` | Plans a single-PR issue or one decision point (DP) as a work instruction. |
| `campaign` | Plans multi-PR work as a campaign plan or release plan, and evaluates completion reports in evaluation mode. |
| `plan-review` | Reviews a planning document (release plan, campaign plan, work instruction) before the user approves it. |
| `control-brief` | Has the controlling session announce the reporting gates to its subordinate sessions. |
| `report-review` | Vets a subordinate session's draft report, commit messages, or PR body before it reaches the user. |
| `doctrine` | Creates or reinforces an execution rule when a decision has no rule or precedent to stand on. |

## Templates

Templates live in `templates/` and are referenced by skills as `${CLAUDE_PLUGIN_ROOT}/templates/<name>.md`.

**Plans**

| Template | Purpose |
| --- | --- |
| `strategy.md` | Release plan for the largest efforts. |
| `campaign.md` | Campaign plan for multi-PR work. |
| `opord.md` | Work instruction for a single PR or one DP. |
| `verbal-order.md` | Five-sentence verbal order for small work, as an issue comment. |
| `frago.md` | Change directive, recorded as an appendix entry of a work instruction. |
| `delegation.md` | Default delegation rules: what an executor decides alone, reports after, or clears first. |

**Reports**

| Template | Purpose |
| --- | --- |
| `report-confirmation.md` | Start confirmation, posted before approval. |
| `report-backbrief.md` | Start report from a subordinate session when it begins a DP. |
| `report-periodic.md` | Progress report at phase transitions, branch triggers, and task completions. |
| `report-immediate.md` | Immediate report when a stated condition occurs and a decision is needed. |
| `report-after-action.md` | After-action note for decisions accumulated into the completion report. |
| `report-debrief.md` | Completion report posted at PR creation. |

## Install

Add this repository as a marketplace, then install the plugin from it:

```
/plugin marketplace add sudopark/mission
/plugin install mission@mission
```

The marketplace is defined in `.claude-plugin/marketplace.json` and points at this repository itself.

## Host requirements

The plugin never names a host project or invokes host scripts. What it needs from the host is defined in [`docs/extension-points.md`](docs/extension-points.md).

| Contract | Summary |
| --- | --- |
| [Writing norms](docs/extension-points.md#writing-norms) | The host owns its wording and file conventions. Without them, the reviewer's judgment applies. |
| [Extension points](docs/extension-points.md#extension-points) | Skills declare three events (`skill-end`, `progress-updated`, `report-posted`). The host may attach hooks; unattached events do nothing. |
| [Report posting](docs/extension-points.md#report-posting) | Reports go out through a channel that notifies the user. The default is a plain issue comment. |
| [Runtime environment](docs/extension-points.md#runtime-environment) | Only `control-brief` needs session-to-session messaging. Other skills run without it. |
| [Workflow skills](docs/extension-points.md#workflow-skills) | Planning skills reference host-provided intake, execution, issue-conventions, pr, and (optionally) orchestration skills. Each has a fallback when absent. |
| [Operations directory](docs/extension-points.md#host-artifacts) | Plan and progress files live in one host directory (default location and override are defined in the linked document). The host should gitignore it. |
