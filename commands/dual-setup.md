---
description: Scaffold this repo for the dual-engine workflow (dirs, tracker doc, standards, test commands)
agent: planner
---

Configure THIS repo for the dual-engine workflow. Explore first, confirm with the user, then write.

## 1. Explore

- `git remote -v`, existing `AGENTS.md` / `GLOSSARY.md` / `docs/agents/` / `.scratch/`
- Build and test commands: read `package.json`, `Cargo.toml`, `pyproject.toml`, `CMakeLists.txt`, `colcon` markers, Makefile — infer how to build and how to run tests. Ask the user only if inference fails.

## 2. Ask (one question per turn, recommended answer first)

- Issue tracker: local markdown under `.scratch/<feature>/issues/` (default) or something else?
- Confirm the inferred build + test commands.

## 3. Write

- `.scratch/specs/` and `.scratch/<feature>/issues/` conventions (dirs created on demand, no empty dirs in git)
- `docs/agents/issue-tracker.md`: location `.scratch/<feature>/issues/`, format = one markdown file per ticket (`NN-slug.md`), blocking edges listed as "Blocked by"
- `docs/agents/domain.md`: glossary lives in `GLOSSARY.md` (repo root), ADRs in `docs/adr/`
- `docs/agents/coding-standards.md`: the confirmed build/test commands + any standards the repo already documents
- If an `AGENTS.md` exists at repo root, append/refresh an `## Agent skills` block with one-line pointers to those three docs. If none exists, ask before creating one.

## 4. Report

Finish by telling the user which bash globs correspond to the test commands (e.g. `pytest*`, `colcon test*`) and remind them they can allowlist those for the `local_builder` agent in `opencode.json`.