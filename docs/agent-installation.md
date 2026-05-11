# Agent Installation Guide

Use this guide when an agent is asked to install the Arch Improvement Skills pack.

## Install Contract

Install every directory under `skills/`:

- `arch-improvement-loop`
- `arch-improvement-memory`
- `arch-improvement-orientation`
- `arch-improvement-review`
- `arch-improvement-fix`
- `arch-improvement-ledger`

Do not install only `arch-improvement-loop`; it is an orchestrator and depends on the narrower skills.

## Codex

Preferred:

1. Use Codex's `skill-installer` flow for this GitHub repository if available.
2. Install all six `skills/arch-improvement-*` paths.
3. Tell the user to restart Codex after installation.

Manual fallback:

```sh
git clone https://github.com/VanDalkvist/arch-improvement-skills.git
cd arch-improvement-skills
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R skills/arch-improvement-* "${CODEX_HOME:-$HOME/.codex}/skills/"
```

Verify:

```sh
find "${CODEX_HOME:-$HOME/.codex}/skills" -maxdepth 2 -path "*/arch-improvement-*/SKILL.md" -print
```

## Claude Code

Personal install:

```sh
git clone https://github.com/VanDalkvist/arch-improvement-skills.git
cd arch-improvement-skills
mkdir -p "$HOME/.claude/skills"
cp -R skills/arch-improvement-* "$HOME/.claude/skills/"
```

Project install:

```sh
git clone https://github.com/VanDalkvist/arch-improvement-skills.git
cd your-project
mkdir -p .claude/skills
cp -R ../arch-improvement-skills/skills/arch-improvement-* .claude/skills/
```

Verify:

```sh
find "$HOME/.claude/skills" -maxdepth 2 -path "*/arch-improvement-*/SKILL.md" -print
find .claude/skills -maxdepth 2 -path "*/arch-improvement-*/SKILL.md" -print 2>/dev/null || true
```

Restart Claude Code if the top-level skills directory did not exist before installation.

## Companion Skill Requirement

The loop references Superpowers and architecture-rule skills by name. If the target agent does not have those skills, stop and install or map equivalents before running the loop. Do not degrade into an unplanned review checklist.
