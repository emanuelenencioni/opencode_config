---
description: Decompose the spec or conversation into tracer-bullet tickets with blocking edges and lane assignments (local/cloud)
agent: planner
---

Break into tickets: $ARGUMENTS (a spec path, or the current conversation if empty). If `docs/agents/issue-tracker.md` is missing, tell the user to run `/dual-setup` first.

## Process

1. Gather context: conversation, spec file if referenced, repo exploration. Use GLOSSARY.md vocabulary; respect ADRs. Look for prefactoring opportunities ("make the change easy, then make the easy change").

2. Draft **tracer-bullet vertical slices**:
   - Each slice cuts a narrow but COMPLETE path through every layer it touches; it is demoable or verifiable on its own.
   - Sized to fit one fresh context window.
   - Prefactoring tickets come first.
   - **Wide refactors are the exception**: expand–contract. Add the new form beside the old, migrate call sites in batches (one ticket per batch, each blocked by the expand), then delete the old form in a contract ticket blocked by all batches.

3. Assign each ticket a **Lane**:
   - `local`: touches ≤ 2 files, clear input/output contract, boilerplate/tests/simple functions, verification command is obvious.
   - `cloud`: cross-module refactors, concurrency, security-critical logic, ambiguous contracts, architectural decisions.
   When unsure, assign `cloud` — escalation is cheaper than a failed local run.

4. **Quiz the user**: present the breakdown as a numbered list (Title / Blocked by / What it delivers / Lane). Ask about granularity, blocking-edge correctness, merges/splits, and lane assignments. Iterate until approved.

5. Publish: one file per ticket under `.scratch/<feature-slug>/issues/NN-slug.md`, numbered in dependency order (blockers first):

```
# NN: <title>

**What to build:** end-to-end behavior from the user's perspective, not a layer list.

**Blocked by:** ticket numbers, or "None (can start immediately)".

**Lane:** local | cloud

**Context Files:** existing repo files the implementer must read (pointers only, no prose duplicates).

**Verification Command:** exact bash command proving the slice works.

**Status:** ready-for-agent

- [ ] Acceptance criterion 1
- [ ] Acceptance criterion 2
```

No file paths or code snippets in prose (paths allowed only under Context Files and Verification Command). Report the ticket directory and suggest `/implement`.