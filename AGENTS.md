# Agent Instructions

This repository contains portable agent skills. Keep it generic and open-source safe.

## Rules

- Do not add machine-specific paths, private repository names, tokens, chat contents, or user-specific memory.
- Keep skills under `skills/<skill-name>/SKILL.md`.
- Keep `arch-improvement-loop` as a thin state-machine orchestrator. Do not move review, fix, memory, or ledger logic into it.
- Install all six `arch-improvement-*` skills as a set. Installing only the loop skill is incomplete.
- Before publishing, check for local coupling:

```sh
grep -R "<project-specific-name>\|<absolute-home-path>\|<private-user-name>\|<private-provider>" -n skills docs README.md AGENTS.md scripts || true
```

- Validate Codex skill frontmatter with `quick_validate.py` when available.

## Agent Install Summary

Codex personal skills go under:

```text
${CODEX_HOME:-$HOME/.codex}/skills/<skill-name>/SKILL.md
```

Claude Code personal skills go under:

```text
$HOME/.claude/skills/<skill-name>/SKILL.md
```

Claude Code project skills go under:

```text
.claude/skills/<skill-name>/SKILL.md
```
