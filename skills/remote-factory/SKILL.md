---
name: remote-factory
description: Use remote-factory, also called re:factory, for autonomous multi-agent software planning, coding, implementation, improvement, research loops, specialist Factory agents, and monitoring Factory-managed runs. Use when the user asks to use remote-factory or factory, delegate coding or planning to Factory, run the Factory CEO, invoke Factory specialist agents, monitor Factory progress, or inspect Factory status, history, backlog, evals, or tmux sessions.
---

# Remote Factory

remote-factory, branded as re:factory, is a local multi-agent software evolution harness. It uses a CEO agent to orchestrate specialist agents that plan, build, verify, score, keep or revert, and archive experiments. Treat the official docs as the true source of truth:

- Official docs: https://akashgit.github.io/remote-factory/
- Official repo: https://github.com/akashgit/remote-factory
- Docs source: https://github.com/akashgit/remote-factory/tree/main/docs

When exact behavior, command choices, or flags matter, read the official docs and run `factory <subcommand> --help` instead of guessing.

Use remote-factory because the user wants it to drive actual coding and planning. Do not manually recreate the Factory workflow when Factory should own the cycle.

## Local Defaults

These instructions are agent-neutral. The reader may be Codex, Claude, or another coding agent; `codex` below refers to the Factory runner preference, not the agent reading this skill.

Use `factory` in commands. If it is not on `PATH`, check for it in `$HOME/.local/bin/factory`.

Always prefer Codex as the Factory runner unless the user explicitly asks for another runner. Use `--runner codex`; do not use a Codex profile.

Authenticate Codex outside Factory:

```bash
codex login
codex login status
```

For API-key auth, use:

```bash
printenv OPENAI_API_KEY | codex login --with-api-key
```

Before launching work, check Factory runner availability:

```bash
factory runners list
```

Use `factory runners list` to confirm the Codex runner exists, but do not treat its Codex auth column as authoritative for OAuth auth; verify auth with `codex login status`. If Codex is not authenticated, stop and report that. Do not silently fall back to any other runner.

## Main Workflows

Use the CEO for full planning or coding cycles:

```bash
factory ceo <idea-or-project> --mode design --runner codex
factory ceo <project-path> --mode improve --runner codex
factory ceo <project-path> --mode improve --focus "<target>" --runner codex
factory ceo <project-path> --refine "<request>" --runner codex
factory tmux <project-path> --loop --runner codex
```

Use `--mode design` when planning should happen before coding. Use `--mode improve` for existing projects. Use `--focus` for one specific target. Use `--refine` for one directed change on an existing Factory project. Use `tmux` for long-running or unattended loops.

## Specialist Agents

The user likes the Factory plugin agents installed with:

```bash
uv run factory install --runner codex
```

Do not reinstall them unless the user asks or agent invocation fails due to missing setup.

Use specialist agents with `factory agent ... --runner codex` when a full CEO cycle is unnecessary or when monitoring/intervening in an active Factory run:

```bash
factory agent researcher --task "<task>" --project <project-path> --runner codex
factory agent strategist --task "<task>" --project <project-path> --runner codex
factory agent builder --task "<task>" --project <project-path> --runner codex
factory agent qa --task "<task>" --project <project-path> --runner codex
factory agent archivist --task "<task>" --project <project-path> --runner codex
```

## Monitoring

Monitor Factory while it runs. Do not fire and forget unless the user explicitly asks for an unattended session.

Factory has support commands for monitoring, steering, backlog management, evals, guards, exports, diffs, explanations, and tmux sessions. Use commands such as `status`, `history`, `summary`, `usage`, `message`, `backlog-*`, `eval`, `guard`, `precheck`, `export`, `diff`, `explain`, `tmux-ls`, and `tmux-stop` as needed. Use `message` to steer active or next-cycle work. Only stop a tmux session when the user asks or continuing would clearly be harmful.
