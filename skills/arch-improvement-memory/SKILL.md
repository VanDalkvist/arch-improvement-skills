---
name: arch-improvement-memory
description: Use when a codebase-improvement cycle needs to read or update project-local lessons, recurring execution failures, review heuristics, or process corrections.
---

# Arch Improvement Memory

## Purpose

Keep the improvement loop self-correcting without depending on a specific machine, user, or repository. Memory is project-local markdown, not global model memory.

## Locate Memory

Use the first existing project-local file:

1. `docs/arch-improvement/memory.md`
2. `.arch-improvement/memory.md`
3. A project-policy equivalent named by local instructions.

If none exists and a cycle finds a repeatable lesson, create the least surprising project-local path. Prefer `docs/arch-improvement/memory.md` when the repo tracks docs; otherwise use `.arch-improvement/memory.md`.

## Read Before Every Cycle

At the start of each improvement cycle:

- Read the memory file if it exists.
- Extract only active lessons relevant to this cycle.
- Treat memory as hints, not proof. Recheck current repo facts before acting.
- If a memory entry conflicts with current source of truth, follow source of truth and log the conflict.

## Write After Learning

Append only durable lessons from the current execution:

- A recurring failure mode.
- A better source-of-truth lookup.
- A repo-specific boundary that changed the fix decision.
- A verification command or smoke path that caught a real issue.
- A mistake in the loop itself that should be avoided next time.

Do not write:

- Secrets, tokens, private chat data, credentials, or full logs with sensitive values.
- Every finding from the review; use the ledger for that.
- Generic advice already covered by Superpowers or arch-rules.

## Entry Format

```markdown
## YYYY-MM-DD - <short lesson>
- Context: <what was being improved>
- Symptom: <what went wrong or what nearly went wrong>
- Cause: <why it happened>
- Use next time: <specific behavior change>
- Evidence: <test, command, file, or review artifact>
```

After appending, reread the file before starting the next cycle.
