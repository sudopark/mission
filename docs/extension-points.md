# Host contracts and extension points

This document is the single authority for what the `mission` plugin expects from the project that installs it (the host project). Every skill and template cites it instead of restating it. The plugin never names a host project, never hardcodes a host path, and never invokes host scripts. Everything project-bound is supplied by the host through the four contracts below.

## Writing norms

The host project owns its writing norms and file conventions. The plugin does not ship them.

Skills assume the host has such norms: how issues, comments, and plan files are worded, and how files are named and laid out. Wherever a skill needs a judgment call about wording or file placement, it defers to "the host project's writing norms" or "the host project's file conventions" without citing a file or section number.

If the host provides no norms, every skill still runs. The judgment calls that would have consulted them fall back to the reviewer's discretion.

## Extension points

Skills never invoke scripts. They only declare named event moments, and the host decides whether anything happens there. Each place where an event can fire carries this exact declaration:

> Extension point: `<event>` fires here. See `docs/extension-points.md`.

The vocabulary is closed. Exactly three events exist:

| Event | Fires when | Typical use |
| --- | --- | --- |
| `skill-end` | A planning skill has finished its work. | Usage recording. |
| `progress-updated` | Plan or progress state changed. | Syncing a task board. |
| `report-posted` | A report went out to the user. | Sending a notification. |

The suggested payload for any event is free text: the event name, the skill that fired it, and the issue or plan file that was touched. The plugin does not enforce a schema, and a host may read as much or as little of it as it wants.

Hosts attach behavior through Claude Code hooks in their own settings. The plugin has no knowledge of what, if anything, is attached.

An unattached event is a no-op. With nothing attached, every event does nothing and the skills run unchanged.

## Report posting

The contract is: post the report through a channel that notifies the user.

Which channel is the host's choice. A host may designate a bot account or an MCP tool so that the user receives a mention notification. When the host specifies nothing, the default is a plain issue comment.

Posting a report fires `report-posted`.

## Runtime environment

The `control-brief` skill needs an environment with session-to-session messaging. Specifically, it needs two things: a way to look up another session's address, and one reserved name for the controlling session.

This is an environment requirement, stated in the skill's preamble. It is not assumed anywhere else in the skill body. A host without this capability can still use every other skill in the plugin.

## Host artifacts

Plan and progress files are host artifacts. They live in one directory inside the host repository. The default is `docs/operations/`; the host may override it in its `CLAUDE.md`.

These files are working files, not committed history, so the host is expected to gitignore the directory. Skills refer to it as "the operations directory" and resolve the actual path from the host's configuration, falling back to the default.

Plugin assets such as templates are separate. Skills reference them as `${CLAUDE_PLUGIN_ROOT}/templates/<name>.md`, never through a host repository path.

## Workflow skills

Planning skills hand off to workflow skills that the host provides. The plugin does not ship them in this stage; a later stage ships defaults for intake, execution, and issue conventions. Each is optional, and every skill names the fallback below when the host has none.

| Workflow skill | Role | When absent |
| --- | --- | --- |
| Intake | Receives an issue and seeds the pre-research brief and the progress file. | The user starts the planning skill directly, and the skill proceeds from the issue body. |
| Execution skill (for example, an implement skill) | Carries the execution reporting duties and the gap-report loop. | The executor follows the reporting contract by hand. |
| Issue conventions | Define the body mirror and the comment protocol. | The skill posts plain comments and keeps the local files authoritative. |
| PR skill | Owns the `in review` and `closed` transitions, invokes campaign evaluation mode after a DP merge, and on a revert moves the DP to `regressed` and posts the immediate report. | The user performs these transitions and posts the report. |
| Orchestration skill (optional) | A session mode that runs DPs back to back. | Nothing is required, except that the campaign skill winds an in-flight run down into the ledger. |
