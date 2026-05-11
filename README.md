# Arch Improvement Skills

Reusable agent skills for running architecture-aware repository review and repair loops.

The pack is intentionally decomposed:

- `arch-improvement-loop` - thin state-machine orchestrator over Superpowers plans.
- `arch-improvement-memory` - project-local improvement memory.
- `arch-improvement-orientation` - source-of-truth and architecture orientation.
- `arch-improvement-review` - severity-ranked review and triage.
- `arch-improvement-fix` - bounded repairs with TDD and verification.
- `arch-improvement-ledger` - durable markdown review/fix log.

The loop is designed for codebases where review should lead to reasonable fixes, not broad style cleanup. Style-only findings are logged, while bounded defects with product, runtime, security, contract, or architecture impact are planned and repaired.

## Requirements

This pack is not standalone. It depends on Superpowers and architecture-rule skills being available in the target agent environment.

Superpowers dependency:

- Source project: https://github.com/obra/superpowers
- Claude plugin page: https://claude.com/plugins/superpowers
- Required skill surface: `superpowers:using-superpowers`, `superpowers:writing-plans`, `superpowers:executing-plans`, `superpowers:test-driven-development`, and `superpowers:verification-before-completion`.

Architecture-rule dependency:

- Install `arch-rules-skills`: https://github.com/VanDalkvist/arch-rules-skills
- Required skill surface: `arch-rules-context`, `arch-rules-slice-planning`, and `arch-rules-implementation-review`.
- The target repository should provide or point to an architecture-rules baseline.

If Superpowers or the architecture-rule skills are missing, the agent must stop and install or map equivalents before running `arch-improvement-loop`. Do not silently run the loop as a plain checklist.

## Install In Codex

Install Superpowers first, or confirm that the Codex Superpowers plugin/skills are already available. The upstream Superpowers Codex guide is here: https://github.com/obra/superpowers/blob/main/docs/README.codex.md

Recommended agent instruction:

```text
First ensure Superpowers is installed and that these skills are available: superpowers:using-superpowers, superpowers:writing-plans, superpowers:executing-plans, superpowers:test-driven-development, superpowers:verification-before-completion.
Then install arch-rules-skills from https://github.com/VanDalkvist/arch-rules-skills.
Install all skills from https://github.com/VanDalkvist/arch-improvement-skills/tree/main/skills into Codex.
Use the Codex skill-installer flow if available. Install every arch-improvement-* skill, not only arch-improvement-loop.
After installation, restart Codex so the skills are discovered.
```

Manual fallback:

```sh
git clone https://github.com/VanDalkvist/arch-improvement-skills.git
cd arch-improvement-skills
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R skills/arch-improvement-* "${CODEX_HOME:-$HOME/.codex}/skills/"
```

Codex skill directories should end up like:

```text
~/.codex/skills/arch-improvement-loop/SKILL.md
~/.codex/skills/arch-improvement-memory/SKILL.md
...
```

## Install In Claude Code

Install the Superpowers plugin first, or confirm equivalent Superpowers skills are already installed: https://claude.com/plugins/superpowers

Claude Code supports personal and project skills as filesystem directories with a `SKILL.md` entrypoint. See the official Claude Code skills docs: https://code.claude.com/docs/en/skills

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

Claude Code should see paths like:

```text
~/.claude/skills/arch-improvement-loop/SKILL.md
.claude/skills/arch-improvement-loop/SKILL.md
```

If the top-level `~/.claude/skills` or `.claude/skills` directory did not exist when Claude Code started, restart Claude Code after creating it.

## Verify

Run basic repository checks:

```sh
find skills -name SKILL.md -maxdepth 3 -print
grep -R "<project-specific-name>\|<absolute-home-path>\|<private-user-name>" -n skills || true
```

For Codex skill schema validation, use the local Codex validator when available:

```sh
for skill in skills/arch-improvement-*; do
  uv run --with pyyaml python "$HOME/.codex/skills/.system/skill-creator/scripts/quick_validate.py" "$skill"
done
```

## License

MIT.
