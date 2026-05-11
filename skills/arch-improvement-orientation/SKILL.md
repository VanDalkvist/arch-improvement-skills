---
name: arch-improvement-orientation
description: Use when starting or refreshing a codebase-improvement cycle and the agent must find the real repository, source of truth, architecture constraints, test surface, and active implementation path.
---

# Arch Improvement Orientation

## Purpose

Prevent reviews and fixes from drifting into the wrong repo, stale docs, or generic advice. Establish the live source of truth before reviewing.

## Orientation Checklist

1. Confirm repository root, current branch, git status, package/runtime files, and test commands.
2. Read local instructions: `AGENTS.md`, `CLAUDE.md`, `.codex/`, `.agents/`, project policy docs, task specs, and active plans.
3. Identify active implementation paths. In multi-root repos, verify which app/package is actually used before reviewing.
4. Locate architecture source:
   - Prefer project-pinned architecture docs and project-owned rules.
   - Then locate a pinned `arch-rules` copy or use the upstream `arch-rules` baseline named by project policy.
   - If only a public upstream exists, use it as a baseline and record that assumption.
5. Build an Architecture Context Map with `arch-rules-context`.
6. Identify verification surfaces: unit tests, integration tests, lint/typecheck/build, manual smoke checks, logs, local services, or provider contracts.
7. Record blockers and assumptions in the ledger before deeper review.

## Architecture Context Map

Keep it short:

```markdown
Architecture Context Map
- Stage: mvp | prod | scale | unknown
- Source docs: ...
- Active paths: ...
- Critical gates: AP-...
- Baseline gates: AP-...
- Not applicable: ...
- Known deviations: ...
- Verification surface: ...
- Open decisions: ...
```

## Critical Rule

Do not start implementation from memory or folder names alone. If the current runtime path, provider contract, or architecture boundary is uncertain, inspect the repo until it is known or mark it as an explicit blocker.
