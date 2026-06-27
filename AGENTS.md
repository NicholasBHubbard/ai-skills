# AGENTS.md

This repo contains personal AI skills. Keep changes small, clear, and useful to the agents that will read them later.

## Repo Layout

- `skills/<name>/SKILL.md` is the skill instruction file.
- `skills/<name>/agents/openai.yaml` is optional Codex/OpenAI UI metadata.
- Add `references/`, `scripts/`, or `assets/` only when they provide real reusable value.

## Skill Style

- Keep skills agent-neutral.
- Keep skills brief. Prefer operational guidance over explanation.
- Link official docs when a tool is external, and tell agents to use local `--help` for exact flags.
- Separate the agent reading the skill from any runner/tool preference inside the skill.
- Do not create auxiliary docs such as README, quick-reference, or changelog files inside a skill.
