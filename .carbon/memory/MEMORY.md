# MEMORY.md — the truth-store seed

> This file is the **seed implementation** of the truth-store contract
> (`contracts/truth-store.md`). It is plain-file source of truth: no database, no
> service. When carbon-substrate lands it will implement the same `read` /
> `write` / `prove` interface over a real store, and everything written against
> the contract keeps working unchanged (contract-and-seed).

## What lives here
Durable, project-level truth that should outlive any single agent session:
- settled decisions (also see `DECISIONS.md`)
- learned facts about the project that the next agent must not relitigate
- pointers to where deeper state lives

## Format
Append short, dated, factual entries. Keys are headings; values are the prose
beneath them. The seed `prove(key)` is `sha256` of the section body.

## Entries

### bootstrap
The carbon floor is installed. Spine provides the laws, the schema, the baseline
skills, and the `validate | lint | install` primitives. This memory file is the
source of truth until substrate replaces it behind the same interface.
