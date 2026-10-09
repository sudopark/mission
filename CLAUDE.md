# mission

A Claude Code plugin extracted from TodoCalendar's plan–order harness (work instructions, campaign plans, release plans, and their reporting contracts). The repo carries exactly one plugin, and the root `.claude-plugin/marketplace.json` points at this repo itself. The GitHub repo is [sudopark/mission](https://github.com/sudopark/mission) and the default branch is `main`. The first install target is a newly started project.

## Language

Everything in this repo is written in English: docs, skills, scripts and their comments, commit messages, PRs, and issues. Upstream material from TodoCalendar is in Korean, so translate it as you migrate it.

## Two sources of truth

- **What moves and what stays** — the body of [sudopark/TodoCalendar#1098](https://github.com/sudopark/TodoCalendar/issues/1098). It lists the migration targets, skills that need splitting, candidates to move along, prerequisites, and open questions. If this repo redraws the boundary, update the #1098 body too — it is the only place where sessions in both repos see the same boundary.
- **What gets moved** — `origin/develop` of the local `~/Documents/codebase/TodoCalendar`. Read it with `git show origin/develop:<path>`, not from the working tree. The working tree may contain uncommitted changes from other sessions.

## Once — initial migration

1. Run `git init` and create the plugin skeleton: `.claude-plugin/plugin.json` (name `mission`), `.claude-plugin/marketplace.json` (plugin source `./`), `skills/`, `README.md`.
2. Read the #1098 body and move the migration targets from `origin/develop`. Invert project-bound values (paths, scripts, board wiring) into injected inputs, per prerequisites 1 and 5.
3. The README explains the origin of this system: chain of command and mission command. The body text uses plain terms (follow the substitutions in TodoCalendar 115fae2d and 9a2b7558).
4. Record the `origin/develop` sha at migration time as the baseline in `UPSTREAM.md` and commit.

## Every session start — sync

TodoCalendar keeps editing the original of this plugin. When a session opens, check for pending upstream changes before any other work.

1. `git -C ~/Documents/codebase/TodoCalendar fetch origin`
2. `git -C ~/Documents/codebase/TodoCalendar log --format='%h %ad %s' --date=short <baseline>..origin/develop -- .claude CLAUDE.md docs/operations/templates`
3. Re-read the body with `gh issue view 1098 -R sudopark/TodoCalendar`. Boundary decisions sometimes change only the body, with no commit.
4. Judge each commit as `applied` or `n/a` and add one row per commit to the log table in `UPSTREAM.md`. The criterion is the migration scope in #1098. A commit that only touches project-specific pieces (test schemes, tuist, board wiring) is `n/a`.
5. When done, move the baseline up to that `origin/develop` sha and commit.

The path filter is deliberately broad. Narrowing it to the migration target list would miss a new skill that joins the planning system before it is added to the list.
