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

## Required Dependencies

This pack depends on Superpowers. Do not run `arch-improvement-loop` until the target environment has the Superpowers plugin or equivalent Superpowers skills installed.

Canonical links:

- Superpowers source project: https://github.com/obra/superpowers
- Claude plugin page: https://claude.com/plugins/superpowers
- Codex installation guide: https://github.com/obra/superpowers/blob/main/docs/README.codex.md

Required Superpowers skill surface:

- `superpowers:using-superpowers`
- `superpowers:writing-plans`
- `superpowers:executing-plans`
- `superpowers:test-driven-development`
- `superpowers:verification-before-completion`

This pack also expects architecture-rule skills equivalent to:

- `arch-rules-context`
- `arch-rules-slice-planning`
- `arch-rules-implementation-review`

If any dependency is missing, stop and install or map an equivalent before installing or running the loop. Do not degrade into a plain review checklist.

## Codex

Preferred:

1. Confirm Superpowers is available in Codex, or install it from https://github.com/obra/superpowers/blob/main/docs/README.codex.md.
2. Use Codex's `skill-installer` flow for this GitHub repository if available.
3. Install all six `skills/arch-improvement-*` paths.
4. Tell the user to restart Codex after installation.

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

Install the Superpowers Claude plugin first, or confirm equivalent Superpowers skills are already installed: https://claude.com/plugins/superpowers

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
