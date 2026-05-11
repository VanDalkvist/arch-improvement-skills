---
name: arch-improvement-review
description: Use when reviewing an existing codebase for an arch-improvement cycle where findings must be severity-ranked, architecture-aware, product-aware, and separated from style-only observations.
---

# Arch Improvement Review

## Purpose

Find defects worth acting on. Lead with impact, not taste.

## Review Scope

Review against:

- Product/user journey failures.
- Correctness and data integrity.
- Security, secrets, privacy, and unsafe side effects.
- Public contracts: APIs, CLIs, schemas, events, files, provider DTOs.
- Architecture gates from the current Architecture Context Map.
- Error handling, config validation, observability, and operability.
- Tests that are missing around changed or risky behavior.

## Finding Format

```markdown
- [P1] <title>
  - Evidence: <file:line, command, failing test, or contract>
  - Impact: <user/product/architecture/runtime consequence>
  - Rule: <AP-ID or project rule, if applicable>
  - Fix decision: fix-now | log-only | blocked
```

Severity:

- P0: data loss, credential leak, destructive side effect, impossible core journey, severe security issue.
- P1: broken core behavior, public contract regression, critical architecture boundary violation.
- P2: real defect with bounded blast radius or missing verification around risky behavior.
- P3: maintainability issue, confusing structure, weak naming, style, or optional cleanup.

## Fix Decision Rules

Fix now when the finding is P0-P2, the repair is bounded, and verification is available.

Log only when the issue is style, broad refactor, speculative design, unclear value, or too large for the current cycle.

Block when the fix needs product approval, architecture change, secret handling, data migration, external side effects, or destructive operations.

## Guardrails

- Do not report style as a bug unless it creates a real maintenance or user risk.
- Do not accept passing tests as proof of architecture compliance.
- Do not recommend a rewrite when a boundary-preserving slice would solve the defect.
- Do not hide assumptions. If evidence is missing, say so in the finding.
