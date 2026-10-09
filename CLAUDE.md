# mission

A general-purpose Claude Code plugin for a plan–order harness: work instructions, campaign plans, release plans, and their reporting contracts. It must not depend on any specific host project. Project-bound values (paths, scripts, board wiring) are injected by the host project, never hardcoded here.

The repo carries exactly one plugin, and the root `.claude-plugin/marketplace.json` points at this repo itself. The GitHub repo is [sudopark/mission](https://github.com/sudopark/mission) and the default branch is `main`.

## Workflow

Never commit to `main` directly. Work on a branch and open a PR, even for small changes.

## Language

Everything in this repo is written in English: docs, skills, scripts and their comments, commit messages, PRs, and issues.
