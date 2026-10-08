# OpenCode Dual-Engine Skill Tree (Plan Big, Build Small)

> Adapted from Matt Pocock's composable agent skills (`mattpocock/skills`)  
> Harness: OpenCode (Anomaly)  
> Architecture: Frontier Planner (Cloud) + Isolated Context Builder (Local Ollama/vLLM)

---

## 1. System Architecture & Context Boundaries

```text
[User Request]
│
▼
┌────────────────────────────────────────────────────────┐
│ PRIMARY AGENT: @planner (Claude Sonnet / Opus)         │
│ - Skills: /grill-with-docs, /to-spec, /to-tickets      │
│ - Reads: Full codebase context, ADRs, GLOSSARY.md      │
│ - Outputs: Tracer-bullet tickets in .scratch/tickets/  │
└──────────────────────────┬─────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
        LANE: cloud                 LANE: local
             │                           │
             │             [Gating Check: Interactive / Auto]
             │                           │
             ▼                           ▼
┌──────────────────────────┐   ┌─────────────────────────────────────────┐
│ Primary Model Implements │   │ SUBAGENT: @local_builder (Qwen 14B/32B) │
│ - Architectural shifts   │   │ - Fresh subagent session                │
│ - High-risk concurrency  │   │ - Context: < 500 tokens (Spec + Test)   │
│ - Cross-module changes   │   │ - Executes: Edits file & runs test      │
└────────────┬─────────────┘   └────────────────────┬────────────────────┘
             │                                      │
             │                     ┌────────────────┴────────────────┐
             │                  Success                            Failure
             │                     │                                 │
             │                     │                  [Auto-Escalation Ladder]
             │                     │                  Reverts git diff and
             │                     │                  routes ticket to @planner
             │                     │                                 │
             └─────────────────────┼─────────────────────────────────┘
                                   ▼
┌────────────────────────────────────────────────────────┐
│ PRIMARY AGENT: /code-review (Frontier Model)           │
│ - Validates git diff against specs and coding rules    │
│ - Verifies no hallucinations, scope creep, or breaks   │
└────────────────────────────────────────────────────────┘
```

---

## 2. Configuration: `opencode.jsonc`

Save this at the project root (`.opencode/opencode.jsonc`) or in `~/.config/opencode/opencode.jsonc`:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "default_agent": "planner",
  "agent": {
    "planner": {
      "mode": "primary",
      "model": "anthropic/claude-sonnet-4-5",
      "prompt": "You are the Lead Architect. You oversee system design, grilling, spec generation, and task breakdown. You never write boilerplate code directly when a task is scoped for local execution. You delegate granular execution tickets to @local_builder.",
      "permission": {
        "edit": "allow",
        "bash": "allow"
      }
    },
    "local_builder": {
      "mode": "subagent",
      "model": "ollama/qwen2.5-coder:14b",
      "prompt": "You are a focused, isolated code implementer. You only receive minimal, single-task ticket prompts. Edit only the specified files, run tests via bash, and exit cleanly. Keep responses under 2 sentences.",
      "permission": {
        "edit": "allow",
        "bash": "allow"
      }
    }
  },
  "permission": {
    "subagent": "ask"
  }
}
```

---

## 3. Directory Layout

```text
repo-root/
├── .opencode/
│   ├── opencode.jsonc
│   └── skills/
│       ├── setup-dual-skills/
│       │   └── SKILL.md
│       ├── grill-with-docs/
│       │   └── SKILL.md
│       ├── to-spec/
│       │   └── SKILL.md
│       ├── to-tickets/
│       │   └── SKILL.md
│       ├── implement-spec/
│       │   └── SKILL.md
│       └── code-review/
│           └── SKILL.md
├── docs/
│   └── agents/
│       ├── issue-tracker.md
│       ├── domain.md
│       └── coding-standards.md
└── .scratch/
    ├── specs/
    └── tickets/
```

---

## 4. Skill Definitions (`SKILL.md`)

### A. Setup: `.opencode/skills/setup-dual-skills/SKILL.md`

```markdown
---
name: setup-dual-skills
description: Initializes repository layout for dual-model planning and execution
user-invoked: true
---

# Setup Dual Skills

Initialize this repository for dual-engine development:
1. Create directory structure:
   - `docs/agents/`
   - `.scratch/specs/`
   - `.scratch/tickets/`
2. Create `docs/agents/issue-tracker.md` with configuration:
   - Location: `.scratch/tickets/`
   - Format: Local markdown tracer-bullet tickets
3. Scan codebase build & test commands and write to `docs/agents/coding-standards.md`.
```

---

### B. Planning: `.opencode/skills/grill-with-docs/SKILL.md`

```markdown
---
name: grill-with-docs
description: Relentlessly interviews user to resolve ambiguity and update domain models
user-invoked: true
---

# Grill With Docs

Run exclusively on the primary frontier model:
1. **Interview**: Interrogate the user about requirements, edge cases, data structures, and failure modes. Ask 1-2 sharp questions at a time. Do not write code.
2. **Update Domain Model**:
   - Record ubiquitous terms in `docs/agents/GLOSSARY.md`.
   - Record architectural trade-offs in `docs/adr/`.
3. Stop only when every branch of the design tree is clear.
```

---

### C. Synthesis: `.opencode/skills/to-spec/SKILL.md`

```markdown
---
name: to-spec
description: Converts grilled conversation into an engineering specification
user-invoked: true
---

# To Spec

Synthesize current conversation into a comprehensive engineering spec:
1. Write `.scratch/specs/{feature-name}.md` containing:
   - Problem statement & core invariants
   - Public API signatures / data models
   - Testing & verification matrix
   - Rollback / failure strategies
```

---

### D. Slicing: `.opencode/skills/to-tickets/SKILL.md`

```markdown
---
name: to-tickets
description: Decomposes a spec into minimal, complexity-tagged tracer-bullet tickets
user-invoked: true
---

# To Tickets

Break down `.scratch/specs/{feature-name}.md` into atomic tickets stored in `.scratch/tickets/TKT-{n}.md`.

### Mandatory Ticket Schema:
```markdown
# Ticket: [TKT-001] [Short title]
- Lane: [local | cloud]
- Target Files: [exact file paths, max 1-2 files]
- Dependencies: [list of prerequisite ticket IDs]
- Verification Command: [exact bash command]

## Implementation Brief (Strictly < 300 words)
- Expected interface/behavior:
- Exact inputs and expected outputs:
- Out of bounds:
```

### Routing Rules for Lane:
- **Assign `Lane: local` if:**
  - Scope touches $\le 2$ files.
  - Adding boilerplate, mock fixtures, or unit tests.
  - Clear input/output specification provided.
- **Assign `Lane: cloud` if:**
  - Broad cross-module refactors.
  - Core concurrency, database migrations, or cryptographic logic.
```

---

### E. Orchestration & Gating: `.opencode/skills/implement-spec/SKILL.md`

```markdown
---
name: implement-spec
description: Orchestrates execution across tickets, delegating local tasks with auto-fallback
user-invoked: true
---

# Implement Spec

Read ready tickets in `.scratch/tickets/`:

1. Build dependency graph and process unblocked tickets in order.
2. **Execution Routing**:
   - If ticket is marked `Lane: cloud`:
     - Primary agent implements directly.
   - If ticket is marked `Lane: local`:
     - Check gating policy:
       `"Dispatch ticket TKT-{n} to local builder? [y/N]"` (Skip if autonomous mode active).
     - **Isolate and Dispatch**:
       Invoke `@local_builder` with ONLY:
       - **Task**: Implement `{TKT-n}`
       - **Target file**: `{target_file}`
       - **Brief**: `{Implementation Brief}`
       - **Verification**: Run `{verification_command}` via bash and exit.
3. **Escalation & Safety Gate**:
   - After `@local_builder` finishes, verify exit status and test run.
   - If test passes: Proceed to next ticket.
   - If test fails or changes corrupt other files:
     - Run `git checkout -- {target_file}` to discard dirty diff.
     - Escalate immediately: Primary agent takes over the ticket directly. Do not loop locally.
4. When all tickets finish, invoke `/code-review`.
```

---

### F. Quality Gate: `.opencode/skills/code-review/SKILL.md`

```markdown
---
name: code-review
description: Frontier model review of working diff against specs and repo standards
user-invoked: true
---

# Code Review

Run on the primary frontier model:
1. Inspect `git diff` against base branch.
2. Audit on two axes:
   - **Spec Compliance**: Does the code fulfill the originating tickets without scope creep?
   - **Local Worker Guardrail**: Check for hallucinated imports, omitted edge cases, or commented-out tests introduced during local execution.
3. Run the full test suite.
4. Present findings to user before creating a git commit.
```

---

## 5. Automated Installation Script

To install all subdirectories, skill files, and configs directly into your project in a single command, run this in your repository root:

```bash
mkdir -p .opencode/skills/{setup-dual-skills,grill-with-docs,to-spec,to-tickets,implement-spec,code-review} docs/agents .scratch/{specs,tickets}

cat << 'EOF' > .opencode/opencode.jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "default_agent": "planner",
  "agent": {
    "planner": {
      "mode": "primary",
      "model": "anthropic/claude-sonnet-4-5",
      "prompt": "You are the Lead Architect. You oversee system design, grilling, spec generation, and task breakdown. You delegate execution tickets to @local_builder.",
      "permission": { "edit": "allow", "bash": "allow" }
    },
    "local_builder": {
      "mode": "subagent",
      "model": "ollama/qwen2.5-coder:14b",
      "prompt": "You are a focused, isolated code implementer. Edit only specified files, run tests via bash, and exit cleanly.",
      "permission": { "edit": "allow", "bash": "allow" }
    }
  },
  "permission": { "subagent": "ask" }
}
EOF

cat << 'EOF' > .opencode/skills/setup-dual-skills/SKILL.md
---
name: setup-dual-skills
description: Initializes repository layout for dual-model planning and execution
user-invoked: true
---
# Setup Dual Skills
1. Create docs/agents/, .scratch/specs/, and .scratch/tickets/.
2. Set .scratch/tickets/ in docs/agents/issue-tracker.md.
3. Add test commands to docs/agents/coding-standards.md.
EOF

cat << 'EOF' > .opencode/skills/grill-with-docs/SKILL.md
---
name: grill-with-docs
description: Relentlessly interviews user to resolve ambiguity and update domain models
user-invoked: true
---
# Grill With Docs
1. Interrogate user on requirements and edge cases (1-2 questions at a time).
2. Update docs/agents/GLOSSARY.md and docs/adr/.
EOF

cat << 'EOF' > .opencode/skills/to-spec/SKILL.md
---
name: to-spec
description: Converts grilled conversation into an engineering specification
user-invoked: true
---
# To Spec
Write .scratch/specs/{feature-name}.md with invariants, public APIs, and test matrix.
EOF

cat << 'EOF' > .opencode/skills/to-tickets/SKILL.md
---
name: to-tickets
description: Decomposes a spec into minimal, complexity-tagged tracer-bullet tickets
user-invoked: true
---
# To Tickets
Create .scratch/tickets/TKT-{n}.md:
- Lane: local (<=2 files, boilerplate/tests) or cloud (architecture/refactor)
- Target Files
- Dependencies
- Verification Command
- Brief (<300 words)
EOF

cat << 'EOF' > .opencode/skills/implement-spec/SKILL.md
---
name: implement-spec
description: Orchestrates execution across tickets, delegating local tasks with auto-fallback
user-invoked: true
---
# Implement Spec
1. If Lane: cloud, primary implements.
2. If Lane: local, dispatch to @local_builder with target file, brief, and test command.
3. If test fails, git checkout -- {file} and escalate to primary agent immediately.
4. Call /code-review when done.
EOF

cat << 'EOF' > .opencode/skills/code-review/SKILL.md
---
name: code-review
description: Frontier model review of working diff against specs and repo standards
user-invoked: true
---
# Code Review
1. Inspect git diff against tickets.
2. Check for hallucinations or scope creep from local runs.
3. Run test suite and report findings before commit.
EOF
```