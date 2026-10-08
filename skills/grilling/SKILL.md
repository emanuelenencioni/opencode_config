---
name: grilling
description: Relentlessly interview the user about a plan, design, or decision until every branch of the design tree is resolved. Use before writing specs, tickets, or code when requirements are unclear or the user asked to be grilled.
---

# Grilling

Interview the user until the design tree has no unexplored branches. Your job is to surface what the user has not said, not to propose solutions.

## Rules

- Ask **1-2 sharp questions at a time**. Never dump a questionnaire.
- Prefer concrete over abstract: ask about data shapes, failure modes, edge cases, concurrency, and what happens when things go wrong.
- Silence is an answer you must fill: if the user skipped something, ask about it next round.
- **Never write code.** No file edits. You may explore the repo to ground questions in reality.
- End the interview only when: inputs/outputs are known, failure modes are named, scope boundaries (in/out) are explicit, and no open "it depends" remains.
- Use the project's domain vocabulary. Unknown terms → load the domain-modeling skill to record them.

## Loop

1. Explore the repo enough to ask informed questions.
2. Ask 1-2 questions. Wait for the answer.
3. Fold answers into your understanding; update the domain model when a new term or decision crystallizes.
4. When a branch resolves, say so in one line ("OK: X is fixed because Y").
5. When every branch is resolved, summarize the agreed design in ≤ 10 bullets and stop.