---
description: Relentlessly interview the user about a plan until every branch of the design tree is resolved, building the domain model as you go
agent: planner
---

Run a grilling session on: $ARGUMENTS

Load the `grilling` skill and follow it. Additionally, load the `domain-modeling` skill: as terms and decisions crystallize during the interview, maintain `GLOSSARY.md` and `docs/adr/` per that skill (propose to the user before writing).

Do not write code. Do not produce a spec — that is `/to-spec`, run it afterwards.