# opencode-config

Personal [OpenCode](https://opencode.ai) configuration: agents, commands, and skills implementing a **dual-engine workflow** (frontier planner + local builder) inspired by [mattpocock/skills](https://github.com/mattpocock/skills).

## Layout

```
opencode.json                       provider + agent definitions
agents/                             custom markdown agents
commands/                           slash commands (the workflow entry points)
skills/                             reusable agent skills (model-invoked)
```

## Dual-engine workflow

Two agents collaborate:

- **`planner`** (primary, frontier model — deepseek-v4.1-flash by default): grills the user, writes specs and tracer-bullet tickets, implements cloud-lane tickets, orchestrates everything, reviews code.
- **`local_builder`** (hidden subagent, local llama.cpp model): implements single local-lane tickets in isolation — one request at a time — with an edit/test/report cycle and a one-attempt-only escalation ladder back to the planner.

Slash commands (run in any repo):

| Command | Purpose |
|---|---|
| `/dual-setup` | Scaffold the repo: ticket dirs, domain docs, build/test commands |
| `/grill` | Socratic interview that also builds the project domain model |
| `/to-spec` | Turn the conversation into an engineering spec (seam analysis first) |
| `/to-tickets` | Decompose the spec into vertical-slice tickets with blocking edges and `Lane: local\|cloud` |
| `/implement` | Work the ticket frontier: planner for cloud tickets, `local_builder` for local ones (strictly sequential), safe rollback on failure |
| `/review` | Two-axis code review (Standards + Spec) as parallel subagents, plus the full test suite |

Supporting skills: `grilling`, `domain-modeling`, `tdd`, `code-review` — the reusable discipline the commands compose.

## Setup

1. Install [opencode](https://opencode.ai), then copy this directory to `~/.config/opencode/`.
2. **Local model**: set the `LOCAL_MODEL_SERVER` environment variable to your llama.cpp OpenAI-compatible endpoint (e.g. `http://localhost:8080/v1`) — see the `provider.llamacpp` block in `opencode.json`.
3. **Frontier model**: authenticate OpenRouter via `opencode auth login` (any frontier model works; the planner's model is one line in `opencode.json`).
4. In a target repo, run `/dual-setup` once, then follow the command chain above.