---
name: domain-modeling
description: Build and sharpen the project's domain model while talking to the user - maintain the ubiquitous language in the domain doc and record hard decisions as ADRs. Use whenever a new term, concept, or architectural trade-off crystallizes during a conversation.
---

# Domain Modeling

Maintain the project's shared language so conversations, code, and docs all use the same words.

## Where things live

Read the layout from `docs/agents/domain.md` if it exists (written by /dual-setup). Default layout:

- `GLOSSARY.md` at repo root — terms and definitions
- `docs/adr/` — one file per hard decision, named `NNNN-short-title.md`

## Glossary discipline

- One entry per term: **term**, one-sentence definition, and (when useful) the term it replaces ("materialization cascade", not "when lessons in a section become real files").
- Never define a term that already has an entry — reuse the existing word everywhere: code names, file names, conversation.
- When the user uses a fuzzy word, challenge it: "Is X the same as Y in GLOSSARY.md, or a different thing?"

## ADR discipline

Write an ADR only for decisions that are hard to reverse or hard to explain (architecture choices, trade-offs, rejected alternatives). Format: context → decision → consequences (3 short sections). Not for every choice.

## Workflow

1. During conversation, watch for: new nouns (→ glossary), new rules connecting nouns (→ glossary or ADR), choices with trade-offs (→ ADR).
2. Propose the term/definition to the user before writing it.
3. Write it immediately after agreement — do not batch for later.