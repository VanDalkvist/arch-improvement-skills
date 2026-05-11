---
name: arch-improvement-fix
description: Use when an arch-improvement review has selected bounded findings for repair and the agent must patch only reasonable defects while preserving architecture boundaries and verification integrity.
---

# Arch Improvement Fix

## Purpose

Repair selected findings without turning the review into a cleanup spree.

## Preconditions

Before editing:

- The finding is marked `fix-now`.
- The relevant architecture context and source-of-truth docs are known.
- The change is bounded to a behavior, contract, boundary, or verification gap.
- Existing unrelated worktree changes have been identified and will not be reverted.

## Fix Flow

1. Convert each fix-now finding into a small slice with `arch-rules-slice-planning`.
2. For behavior changes and bugfixes, use `superpowers:test-driven-development`:
   - Write or update the smallest test that exposes the problem.
   - Run it and confirm the expected failure.
   - Implement the smallest boundary-preserving fix.
   - Rerun the targeted test.
3. For non-code fixes such as docs or config examples, define a concrete validation command or inspection check.
4. Run `arch-rules-implementation-review` on touched boundaries.
5. Run broader verification only when the change touches shared behavior or public contracts.
6. Send every skipped or deferred item to the ledger with the reason.

## Reasonable Patch Standard

Patch:

- Incorrect behavior.
- Broken or ambiguous public contracts.
- Missing validation that can cause runtime failure.
- Architecture boundary violations with concrete impact.
- Missing tests around a fixed defect.
- Security, privacy, or secret-handling issues.

Do not patch by default:

- Formatting preferences already covered by tooling.
- Renames without user or architecture value.
- Optional abstractions.
- Large refactors that only make the code feel cleaner.
- Unrelated failures discovered while testing, unless they block verification; log them separately.

## Verification Evidence

Every fix must end with:

- Tests or smoke checks run.
- Architecture gates checked.
- Files changed.
- Residual risk.

If verification cannot be run, state why and log the unverified risk.
