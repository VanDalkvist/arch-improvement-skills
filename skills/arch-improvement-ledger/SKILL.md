---
name: arch-improvement-ledger
description: Use when a arch-improvement cycle needs durable markdown records of findings, fix decisions, patches, skipped style issues, verification, residual risk, and cycle outcomes.
---

# Arch Improvement Ledger

## Purpose

Keep detailed review and repair history outside the chat. The final user answer stays short; the ledger carries the trace.

## Locate Ledger

Use the first existing project-local file:

1. `docs/arch-improvement/review-log.md`
2. `.arch-improvement/review-log.md`
3. A project-policy equivalent named by local instructions.

If none exists, create the same directory family selected by `arch-improvement-memory`.

## What To Record

Append one section per cycle:

```markdown
## Cycle N - YYYY-MM-DD - <scope>

### Context
- Branch:
- Source docs:
- Architecture gates:
- Memory entries used:

### Findings
- [P1] <title> - fix-now | log-only | blocked
  - Evidence:
  - Impact:
  - Rule:

### Fixes Applied
- <finding title>
  - Changed:
  - Tests:
  - Architecture check:

### Logged Only
- <item> - <why it was not fixed>

### Verification
- Command/check:
- Result:

### Residual Risk
- ...

### Lessons For Memory
- ...
```

## Logging Rules

- Log all findings, including style-only and deferred items, but clearly mark them `log-only`.
- Keep sensitive data out of the ledger. Redact tokens, secrets, chat contents, private user data, and provider credentials.
- Include enough evidence that a later agent can recheck the issue without trusting the prior agent.
- If the repo uses issue tracker IDs, include them, but do not require any external system.

## Final Response Shape

Summarize:

- Fixed: count and titles.
- Logged only: count.
- Blocked: count and reason.
- Verification: commands/checks.
- Ledger path.
