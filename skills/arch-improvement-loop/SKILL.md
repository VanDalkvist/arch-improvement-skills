---
name: arch-improvement-loop
description: "Use when asked to repeatedly review and improve an existing codebase, especially when the work must combine Superpowers, architecture rules, feedback, fixes, and durable markdown logging. Триггеры: «придумай как сделать», «арх ревью», «архитектурное ревью», «арх луп», «почини через арх луп», «рефакторинг», «сделай фичу», «добавь сервис», «создай модуль», «анализ кодовой базы»."
---

# Arch Improvement Loop

## Purpose

Run a bounded review -> fix -> verify -> learn cycle for any repository. This is a thin orchestrator; delegate real work to the narrower skills below instead of expanding this skill into a god-skill.

## Runtime Contract

This skill is a state machine over Superpowers plans. Do not treat the cycle as an informal checklist.

Hard rules:

- No code edits before a written Superpowers plan exists for the selected fixes.
- No plan before memory, orientation, and architecture context are loaded.
- No fix execution outside the plan, except to unblock a verification command; log the exception.
- No state transition without producing that state's required artifact.
- The orchestrator only routes states. Narrow skills do the work.

## State Machine

| State | Required skills | Required artifact | Allowed next states |
| --- | --- | --- | --- |
| S0 Bootstrap | `superpowers:using-superpowers`, local project instructions | Repo root, branch, dirty worktree notes | S1 |
| S1 Memory Load | `arch-improvement-memory` | Active memory lessons or `none` | S2 |
| S2 Orientation | `arch-improvement-orientation`, `arch-rules-context` | Architecture Context Map | S3 |
| S3 Brainstorm | `superpowers:brainstorming` | Approved design from user (hard-gate) | S4 |
| S4 Review | `arch-improvement-review`, `arch-rules-implementation-review` | Severity-ranked findings | S5 |
| S5 Triage Gate | `arch-improvement-review`, `arch-improvement-ledger` | `fix-now`, `log-only`, `blocked` decisions | S6 if fix-now exists; S10 if none |
| S6 Plan Write | `superpowers:writing-plans`, `arch-rules-slice-planning` | Saved Superpowers plan with checkbox steps | S7 |
| S7 Plan Review | `superpowers:executing-plans` | Critical review of the plan before execution | S8 if plan is sound; S6 if plan has gaps; S11 if blocked |
| S8 Execute Fix Slice | `arch-improvement-fix`, `superpowers:test-driven-development` | Red/green evidence or documented non-code validation | S9 per slice; S8 for next slice |
| S9 Verify Gate | `superpowers:verification-before-completion`, `arch-rules-implementation-review`, `arch-improvement-product-review` | Fresh verification evidence (dual-circuit PASS) | S10 if all selected fixes verified; S8 if a planned slice remains; S11 if blocked |
| S10 Ledger Update | `arch-improvement-ledger` | Cycle section in markdown ledger | S11 |
| S11 Learn Gate | `arch-improvement-memory` | New memory entry or explicit `no durable lesson` | S1 for another cycle; S12 to stop |
| S12 Stop | `arch-improvement-ledger` | Final summary with ledger path | terminal |

## Planning Rules

In S6, use `superpowers:writing-plans` to create a cycle plan. Save it to the Superpowers plan location unless local instructions override it:

```text
docs/superpowers/plans/YYYY-MM-DD-arch-improvement-cycle-N.md
```

The plan must include:

- Goal for this cycle.
- Architecture Context Map reference.
- Findings selected for `fix-now`.
- Explicit exclusions for `log-only` and `blocked` findings.
- One task per bounded fix slice.
- For every code behavior change: failing test step, expected failure, minimal implementation step, passing test step.
- For every non-code fix: concrete validation command or inspection check.
- Ledger and memory update steps at the end.

Do not execute S8 until S7 has critically reviewed the saved plan.

## Execution Rules

In S7/S8, execute the saved plan with `superpowers:executing-plans` by default. If the active environment and user instruction explicitly permit subagents, `superpowers:subagent-driven-development` may replace inline execution, but the state machine and ledger requirements still apply.

During S8:

- Run only planned fix slices.
- If a new defect appears, do not silently expand scope. Add it to the ledger and either return to S6 for a plan revision or leave it `log-only`.
- Each behavior or bugfix uses `superpowers:test-driven-development` unless impossible; if impossible, record why and what validation replaced it.
- Style-only issues remain `log-only` unless they directly block verification or violate an architecture gate.

### Two-Circuit Parallel Verification (S9)

In state S9 (Verify Gate), the orchestrator launches two parallel independent subagents:

1. **Architectural Reviewer** (`arch-rules-implementation-review`):
   - Verifies architectural boundaries, AP rules compliance, line budgets (AP-019), and test determinism.
2. **Product Reviewer** (`arch-improvement-product-review`):
   - Verifies end-to-end user scenario completeness, expectations vs. reality, and absence of UX/CLI blind spots or unhandled states.

The S9 gate is passed ONLY when BOTH reviewers issue `VERDICT: PASS`. If either reviewer reports `FAIL`:
- Findings are triaged (`fix-now`, `log-only`, `blocked`).
- Unresolved P0/P1/P2 issues return to S8 (or trigger a plan revision in S6). Any open P0, P1, or P2 finding triggers `VERDICT: FAIL` in the review gate.
- Advancing to S10 (Ledger Update) is blocked until both circuits pass or the failure is explicitly marked `blocked` with user approval.

## Triage Rules

Fix now:

- P0/P1/P2 defects with concrete user, contract, runtime, security, data, or architecture impact.
- Missing tests only when they protect a selected fix or risky public contract.

Log only:

- Style, naming, optional refactors, speculative improvements, broad redesigns, and low-value cleanup.

Blocked:

- Destructive or externally visible changes.
- Credential, secret, data migration, or provider-account changes.
- Changes that require product or architecture approval.

## Stop Conditions

Stop the current cycle when:

- No remaining fix-now items are bounded enough to repair safely.
- Verification fails for reasons outside the selected slice.
- The next change would require product or architecture approval.
- A critical architecture rule conflicts with the proposed direction.

## Output

Report the current cycle number, fixed items, logged-only items, verification evidence, and the path to the markdown ledger. Keep the answer short; the ledger holds the detail.
