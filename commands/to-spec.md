---
description: Turn the current conversation into an engineering spec - no interview, pure synthesis
agent: planner
---

Synthesize the current conversation (and: $ARGUMENTS, if given) into a spec. Do NOT interview the user; use what you already know. If `docs/agents/issue-tracker.md` is missing, tell the user to run `/dual-setup` first.

## Process

1. Explore the repo if not already explored. Use GLOSSARY.md vocabulary; respect existing ADRs.
2. Sketch the **seams** where the feature will be tested. Prefer existing seams; use the highest possible; fewest wins. Confirm the seams with the user before writing.
3. Write `.scratch/specs/<feature-slug>.md` using this template. **No file paths, no code snippets** (they go stale). Exception: a snippet that encodes a decision better than prose (state machine, schema, type shape).

```
## Problem Statement
From the user's perspective.

## Solution
From the user's perspective.

## User Stories
Numbered, extensive: "As an <actor>, I want <feature>, so that <benefit>" — cover all aspects.

## Implementation Decisions
Modules built/modified, module interfaces, API contracts, schema changes, technical clarifications, architectural trade-offs.

## Testing Decisions
What makes a good test here (external behavior only), which modules are tested, prior art for tests in the codebase.

## Out of Scope

## Further Notes
```

4. Report the spec path to the user and suggest running `/to-tickets`.