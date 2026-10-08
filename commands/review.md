---
description: Two-axis code review (Standards + Spec) of the diff since a fixed point, plus full test suite
agent: planner
---

Review the changes since: $ARGUMENTS (a commit SHA, branch, tag, or `main` — if empty, ask for the fixed point).

1. Load the `code-review` skill and follow it exactly: pin the fixed point, find spec/standards sources, run the Standards and Spec subagents **in parallel**, aggregate per-axis without reranking.
2. Then run the repo's full test suite (from `docs/agents/coding-standards.md` if present) and report pass/fail counts.
3. Present: `## Standards` findings, `## Spec` findings, `## Test suite` result, and a one-line summary per axis. Do not fix anything — wait for the user's decision.