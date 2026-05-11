---
name: arch-improvement-loop
description: Use when asked to repeatedly review and improve an existing codebase, especially when the work must combine Superpowers, architecture rules, feedback, fixes, and durable markdown logging.
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
| S3 Review | `arch-improvement-review`, `arch-rules-implementation-review` | Severity-ranked findings | S4 |
| S4 Triage Gate | `arch-improvement-review`, `arch-improvement-ledger` | `fix-now`, `log-only`, `blocked` decisions | S5 if fix-now exists; S9 if none |
| S5 Plan Write | `superpowers:writing-plans`, `arch-rules-slice-planning` | Saved Superpowers plan with checkbox steps | S6 |
| S6 Plan Review | `superpowers:executing-plans` | Critical review of the plan before execution | S7 if plan is sound; S5 if plan has gaps; S10 if blocked |
| S7 Execute Fix Slice | `arch-improvement-fix`, `superpowers:test-driven-development` | Red/green evidence or documented non-code validation | S8 per slice; S7 for next slice |
| S8 Verify Gate | `superpowers:verification-before-completion`, `arch-rules-implementation-review` | Fresh verification evidence | S9 if all selected fixes verified; S7 if a planned slice remains; S10 if blocked |
| S9 Ledger Update | `arch-improvement-ledger` | Cycle section in markdown ledger | S10 |
| S10 Learn Gate | `arch-improvement-memory` | New memory entry or explicit `no durable lesson` | S1 for another cycle; S11 to stop |
| S11 Stop | `arch-improvement-ledger` | Final summary with ledger path | terminal |

## Planning Rules

In S5, use `superpowers:writing-plans` to create a cycle plan. Save it to the Superpowers plan location unless local instructions override it:

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

Do not execute S7 until S6 has critically reviewed the saved plan.

## Execution Rules

In S6/S7, execute the saved plan with `superpowers:executing-plans` by default. If the active environment and user instruction explicitly permit subagents, `superpowers:subagent-driven-development` may replace inline execution, but the state machine and ledger requirements still apply.

During S7:

- Run only planned fix slices.
- If a new defect appears, do not silently expand scope. Add it to the ledger and either return to S5 for a plan revision or leave it `log-only`.
- Each behavior or bugfix uses `superpowers:test-driven-development` unless impossible; if impossible, record why and what validation replaced it.
- Style-only issues remain `log-only` unless they directly block verification or violate an architecture gate.

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
