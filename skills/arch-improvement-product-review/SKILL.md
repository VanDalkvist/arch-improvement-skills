---
name: arch-improvement-product-review
description: Use when independently reviewing an implementation slice or codebase against user/product requirements, verifying end-to-end user journeys, UX completeness, edge cases, and failure feedback.
---

# Arch Improvement Product Review

## Purpose

Independently verify whether the product or user problem has actually been solved, rather than merely checking whether tests pass or architecture rules are met.

Lead with real user impact, completeness of the user journey, and honesty about failure modes.

## When To Use

- In state S9 of `arch-improvement-loop` as part of the parallel dual-contour verification gate (running concurrently with `arch-rules-implementation-review`).
- When evaluating any feature, bugfix, or refactor to verify that the end-to-end user or operator experience remains intact, coherent, and free of silent regressions.
- When an implementation passes unit tests but needs verification against actual user expectations, CLI/UI ergonomics, and edge states.

## Mandatory Analysis

Every product review must systematically evaluate four dimensions:

### 1. Intent & Problem Discovery
Identify the root user/operator problem and expected outcomes:
- Extract the core user goal from the plan, issue, PRD, or user request.
- Identify the persona or caller (end user, CLI operator, API consumer, subagent).
- Define what constitutes complete success from their perspective.

### 2. End-to-End User Journey Tracing
Trace the entire execution path step-by-step:
- **Input:** How does the user trigger or configure the action? Are arguments, flags, and inputs intuitive and validated early?
- **Reaction & Feedback:** Does the system provide clear, immediate, and continuous progress feedback during work? Does it avoid hanging silently?
- **Result:** Is the delivered outcome complete, legible, and directly usable without manual cleanup or guesswork?
- **Errors:** How do failures surface? Are error messages actionable, explaining what went wrong and how the user can recover?

### 3. UX & State Completeness
Check for common operational and UX blind spots:
- **Silent Swallows:** Ensure no errors, rejected promises, non-zero subprocess exits, or edge cases are silently suppressed or replaced with fake success.
- **Lost Feedback:** Verify long-running operations do not leave the user in the dark.
- **Unclosed & Edge States:**
  - Empty data or zero search results (does it clearly state "no items found" or crash/display blankness?).
  - Cancellation or abort (does Ctrl-C / abort signal terminate cleanly without leaving corrupt state or zombie processes?).
  - Degraded or offline mode (network timeouts, service unavailable).
  - Background/daemon modes (proper status reporting, logs, and PID/socket lifecycle).
- **Misleading Output:** Verify that output accurately reflects actual state (e.g., no "saved" message before persistence succeeds).

### 4. Severity Classification (User-Impact Focused)

- **P0 (Catastrophic / Blocking):**
  - Core user journey is broken or impossible to complete.
  - Data loss, corruption, or irreversible destructive action without confirmation.
  - False success: reporting success while the underlying operation failed or dropped data.
- **P1 (Critical UX / Journey Defect):**
  - Major user journey failure or unexpected blocker in primary flows.
  - Silent failure leaving the user unable to know what happened or recover.
  - Broken contract with the operator/caller (missing output, wrong exit code, corrupted payload).
- **P2 (Degraded Experience / Edge Defect):**
  - Secondary user journey defect or unhandled edge condition (empty lists, cancelled state).
  - Confusing, vague, or misleading error messages.
  - Missing progress feedback during long operations where work still eventually succeeds.
- **P3 (Cosmetic / Minor Ergonomics):**
  - Minor text or formatting inconsistencies.
  - Suboptimal default value or slight CLI/UI ergonomics polish.

## Canonical Output Format

Every product review must strictly format its findings and verification block:

```markdown
Findings
- [P1/P2/P3] <title>
  - Scenario: <пользовательский шаг, где происходит сбой или непонимание>
  - Expectation: <что легитимно ожидает пользователь/оператор>
  - Reality: <что происходит фактически>
  - Fix decision: fix-now | log-only | blocked

Product Verification
- User Journey Covered: YES | PARTIAL | NO
- Missing UX States: ...
- Residual User Risk: ...

VERDICT: PASS | FAIL
```

If no defects are found, the `Findings` section should state:
```markdown
Findings
- None
```

## Verdict Rules

- **VERDICT: FAIL** if:
  - Any unresolved P0, P1, or P2 findings exist (P0, P1, and P2 findings all fail the review gate).
  - `User Journey Covered` is `NO` or `PARTIAL` with unverified critical steps.
  - Silent swallows or false-positive success states are detected in the active slice.
- **VERDICT: PASS** only when:
  - All target user journeys are thoroughly covered (`User Journey Covered: YES`).
  - No open P0, P1, or P2 findings exist.
  - Any P3 findings (minor polish or cosmetic ergonomics) are triaged with explicit `Fix decision` and residual user risk is clearly documented and acceptable.

## Guardrails

- Do not substitute passing unit or mock tests for real user journey tracing.
- Do not excuse silent swallows as "clean error handling".
- Do not assume CLI or API callers know internal implementation details; evaluate errors from the caller's perspective.
- Always be explicit about unverified scenarios when environments or mocks limit end-to-end execution.
