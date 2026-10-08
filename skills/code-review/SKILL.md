---
name: code-review
description: Two-axis review of the diff since a fixed point - Standards (repo conventions + a smell baseline) and Spec (faithfulness to the originating spec/tickets). Runs both reviews as parallel subagents and reports them side by side. Use after implementing tickets or whenever the user asks to review changes.
---

# Code Review

Two-axis review of the diff between `HEAD` and a fixed point the user supplies (or the base of the working branch).

- **Standards**: does the code follow the repo's documented standards?
- **Spec**: does it faithfully implement the originating spec/tickets?

Both axes run as **parallel subagents** so they do not pollute each other's context. Never merge or rerank their findings.

## Process

### 1. Pin the fixed point

User-supplied ref, or ask. Capture `git diff <fixed-point>...HEAD` (three-dot) and `git log <fixed-point>..HEAD --oneline`. Verify the ref resolves (`git rev-parse`) and the diff is non-empty **before** spawning subagents; fail here, not inside them.

### 2. Find sources

- **Spec source**, in order: spec/ticket paths mentioned in commits → user argument → newest file in `.scratch/specs/` or `.scratch/*/issues/` matching the feature → ask. If none: the Spec axis reports "no spec available".
- **Standards sources**: any file documenting how to write code (`CLAUDE.md`, `AGENTS.md`, `docs/agents/coding-standards.md`, `CONTRIBUTING.md`).

### 3. Spawn both subagents in parallel

Give each the diff command, commit list, and sources, plus its brief:

**Standards brief**: "Report per hunk: (a) documented-standard violations, citing file + rule; (b) any baseline smell below, named and quoted. Documented standards override the baseline. Smells are judgement calls, never hard violations. Skip anything tooling enforces. Under 400 words."

**Spec brief**: "Report: (a) requirements missing or partial; (b) behavior nobody asked for (scope creep); (c) implemented-but-suspect behavior. Quote the spec/ticket line for each finding. Under 400 words."

### 4. Smell baseline (paste into Standards subagent)

Each smell: what it is → fix. Judgement calls only.

- **Mysterious Name**: name does not reveal purpose → rename; no honest name means murky design.
- **Duplicated Code**: same logic shape in 2+ hunks → extract the shared shape.
- **Feature Envy**: method reads another object's data more than its own → move it onto the data.
- **Data Clumps**: same few fields travel together everywhere → bundle into one type.
- **Primitive Obsession**: primitive stands in for a domain concept → give the concept its own small type.
- **Repeated Switches**: same switch/if-cascade recurs → polymorphism or one shared map.
- **Shotgun Surgery**: one logical change scattered over many files → gather into one module.
- **Speculative Generality**: abstraction the spec does not need → delete it.
- **Message Chains**: long `a.b().c().d()` walks → hide behind one method.
- **Middle Man**: mostly just delegates → cut it, call the target direct.
- **Commented-out or skipped tests**: introduced in this diff → treat as a hard violation, investigate why.

### 5. Aggregate

Present `## Standards` and `## Spec` verbatim, then one line per axis: total findings + worst issue in that axis. Do not pick a cross-axis winner. Do not fix anything yet - findings go to the user first.