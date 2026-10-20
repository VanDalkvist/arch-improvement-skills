---
name: arch-improvement-product-review
description: Use when independently reviewing an implementation slice or codebase against user/product requirements, verifying end-to-end user journeys, UX completeness, edge cases, and failure feedback.
---

# Arch Improvement Product Review

## Purpose

Independently verify whether the product or user problem has actually been solved, rather than merely checking whether tests pass or architecture rules are met.

Lead with real user impact, completeness of the user journey, and honesty about failure modes.

## The Iron Laws of Product Review

```
1. NO TOOL EXECUTION = INVALID REVIEW (REJECTED AS HALLUCINATION)
2. SPEC-TO-CODE DIFF IS MANDATORY (EVERY OMISSION IS A DEFECT)
3. ADVERSARIAL RED-TEAMING (PRESUMPTION OF DEFECT)
```

- Any review that completes with zero tool executions (`bash`, `read`, inspection scripts) or lacks terminal execution receipts is **automatically disqualified and rejected**.
- Reviewers must approach the implementation adversarially: assume bugs exist and actively search for edge cases, race conditions, double triggers, and misleading output.

## When To Use

- In state S9 of `arch-improvement-loop` as part of the parallel dual-contour verification gate (running concurrently with `arch-rules-implementation-review`).
- When evaluating any feature, bugfix, or refactor to verify that the end-to-end user or operator experience remains intact, coherent, and free of silent regressions.
- When an implementation passes unit tests but needs verification against actual user expectations, CLI/UI ergonomics, and edge states.

## Mandatory Analysis & Verification Steps

Every product review must systematically execute four concrete steps:

### Step 1: Spec-to-Code Gap Analysis (Diff against Plan/PRD)
- Read the active plan/PRD/specification and run `git diff <base>..HEAD` to compare requirements line-by-line.
- Check: Did the implementation omit any specified header, envelope, argument, schema field, or error status?
- Any gap between the specification and actual code is automatically classified as a defect ([P1] or [P2]).

### Step 2: Adversarial User Journey Tracing & Active Probing
Trace and actively execute commands verifying the primary and edge flows:
- **Input:** How does the user trigger or configure the action? Are arguments, flags, and inputs intuitive and validated early?
- **Reaction & Feedback:** Does the system provide clear, immediate, and continuous progress feedback during work? Does it avoid hanging silently?
- **Result & Raw Payload Inspection:** What does the user or caller LLM **literally see**? Inspect the exact strings/payloads. Does it leak internal IDs (e.g. `child-subagent:...`), raw stack traces, or unformatted blobs? Is there a clear envelope (e.g. `[Subagent Reports Available]:\n\n`)?
- **Errors & Failures:** What happens when the background task or subagent fails or aborts? Does the caller receive an actionable report or get left in the dark?

### Step 3: Operational & UX Blind Spots Audit
- **Double Invocations & Split Ownership:** Does more than one component initiate the same action (e.g. both tool body and background task submitting to the same child conversation)?
- **Silent Swallows:** Ensure no errors, rejected promises, non-zero subprocess exits, or edge cases are silently suppressed or replaced with fake success.
- **Unclosed & Edge States:**
  - Empty data or zero search results (does it clearly state "no items found" or crash/display blankness?).
  - Cancellation or abort (clean termination without leaving corrupt state or zombie processes).
  - Background/daemon modes (proper status reporting, logs, and PID/socket lifecycle).

### Step 4: Severity Classification (User-Impact Focused)

- **P0 (Catastrophic / Blocking):**
  - Core user journey is broken or impossible to complete.
  - Data loss, corruption, or irreversible destructive action without confirmation.
  - False success: reporting success while the underlying operation failed or dropped data.
- **P1 (Critical UX / Journey Defect):**
  - Major user journey failure, race condition, or unexpected blocker in primary flows.
  - Double submission / split ownership of execution.
  - Silent failure leaving the user unable to know what happened or recover.
  - Broken contract with the operator/caller (missing output, wrong exit code, corrupted payload).
- **P2 (Degraded Experience / Edge Defect):**
  - Missing envelope/header that causes caller confusion or LLM misinterpretation.
  - Secondary user journey defect or unhandled edge condition (empty lists, cancelled state).
  - Confusing, vague, or misleading error messages.
  - Leaking internal developer jargon / IDs into user-facing output.
  - Missing progress feedback during long operations where work still eventually succeeds.
- **P3 (Cosmetic / Minor Ergonomics):**
  - Minor text or formatting inconsistencies.
  - Suboptimal default value or slight CLI/UI ergonomics polish.

## Canonical Output Format

Every product review must strictly format its findings, tool execution receipts, and verification block:

```markdown
# Продуктовое ревью [S9b: Product Verify Gate]

## 1. Terminal Execution Receipts
```bash
# Paste raw terminal command(s) and their actual output tail here:
$ git diff --stat <base>..HEAD
...
$ node --test <test-file>
...
```

## 2. Spec-to-Code Gap Analysis
- Documented Spec vs. Actual Implementation comparison notes.

## 3. Findings
- [P1/P2/P3] <title>
  - Scenario: <пользовательский шаг, где происходит сбой или непонимание>
  - Expectation: <что легитимно ожидает пользователь/оператор по спецификации>
  - Reality: <что происходит фактически>
  - Fix decision: fix-now | log-only | blocked

(If no defects are found, state: "Findings: - None")

## 4. Product Verification
- User Journey Covered: YES | PARTIAL | NO
- Missing UX States: ...
- Residual User Risk: ...

## Итоговый вердикт
VERDICT: PASS | FAIL
```

## Verdict Rules

- **VERDICT: FAIL** if:
  - Any unresolved P0, P1, or P2 findings exist (P0, P1, and P2 findings all fail the review gate).
  - Terminal Execution Receipts are missing or show zero tool calls.
  - `User Journey Covered` is `NO` or `PARTIAL` with unverified critical steps.
  - Spec-to-Code gaps exist without explicit documented rationale.
  - Silent swallows or false-positive success states are detected in the active slice.
- **VERDICT: PASS** only when:
  - Terminal Execution Receipts confirm real commands were executed.
  - All target user journeys are thoroughly covered (`User Journey Covered: YES`).
  - No open P0, P1, or P2 findings exist.
  - Any P3 findings (minor polish or cosmetic ergonomics) are triaged with explicit `Fix decision` and residual user risk is clearly documented and acceptable.

## Guardrails

- A review with zero tool executions is automatically disqualified.
- Do not substitute passing unit or mock tests for real user journey tracing.
- Do not excuse silent swallows as "clean error handling".
- Inspect raw strings: verify what the user/caller actually sees.
