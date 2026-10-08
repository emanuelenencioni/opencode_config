---
description: Orchestrate implementation of tickets across lanes - planner handles cloud tickets, local_builder handles local tickets one at a time, with safe escalation
agent: planner
---

Implement the tickets in: $ARGUMENTS (a spec path, a ticket directory like `.scratch/<feature>/issues/`, or the newest `.scratch/*/issues/` if empty). If `docs/agents/issue-tracker.md` is missing, tell the user to run `/dual-setup` first.

## Setup

1. Read all tickets; build the dependency graph. Load the `tdd` skill — you use it for your own cloud-lane work.
2. Verify the repo is on the intended branch. Record which ticket is which lane.

## Work the frontier, strictly sequentially

A ticket is **ready** when all its blockers are done. Pick exactly ONE ready ticket at a time. Never run two `local_builder` dispatches concurrently — the local server handles one request at a time.

### Local lane

Before dispatching:
- Run `git status --porcelain -- <target files>` and record the result in your notes (CLEAN / DIRTY per file).
- If a target file is DIRTY, ask the user whether to proceed without auto-revert protection or skip the ticket.

Dispatch to the `local_builder` subagent with a SPARSE brief — context pointers, not duplicated prose:
- Task: implement ticket `<path to ticket file>` (the subagent reads it)
- Target files and Context Files from the ticket
- Verification Command from the ticket
- "Report PASS or FAIL with a one-line reason."

After it returns:
- **PASS** + verification command actually run: mark ticket done, commit the change on the integration branch (reference the ticket number in the message), move to the next frontier ticket.
- **FAIL**: ONE local attempt only, never a retry loop. Revert policy:
  - Target files were CLEAN at dispatch → `git checkout -- <target files>`, then escalate: implement the ticket yourself (load `tdd`).
  - Target files were DIRTY at dispatch → do NOT touch those files; escalate directly, leaving the builder's partial changes for review.
- Escalated tickets keep lane `local` in the file but get a note: `**Escalated to planner:** <one-line reason>`.

### Cloud lane

You implement it yourself with the `tdd` skill: failing test first, small slices, green before the next slice. Commit per slice.

## Close out

When every ticket is done (including escalations):
1. Load the `code-review` skill against the base branch as the fixed point.
2. Fix findings from the review in a single pass (yourself — this is cloud work), re-run the verification suite.
3. Present to the user: per-ticket status table (done / escalated / failed), review findings summary, and the final diff. **Do not commit or push beyond what was already committed per-ticket without explicit user approval.**