---
name: llm-wiki
description: >
  Bootstraps and maintains a Karpathy-style LLM Wiki workspace — a persistent, Markdown-based
  knowledge artifact that an LLM agent incrementally builds from immutable raw sources. 
  ALWAYS use whenever Ivan wants to build an LLM Wiki, ingest sources,
  query the wiki, or perform health checks (lint). Also trigger on
  "Karpathy Wiki", "LLM Wiki", "compile-once", "wiki workspace", "obsidian rag", "/ingest", "/query",
  "/lint", "/clip", "/file", "/search", "/reindex", "/bootstrap". 
  Includes 8 slash commands, a complete workspace bootstrap, and the schema document (CLAUDE.md).
allowed-tools:
  - Read
  - Write(wiki/**)
  - Glob
  - Grep
  - Bash
---

# LLM Wiki Skill

Implements the **Karpathy LLM Wiki Pattern**: A persistent, compounding knowledge artifact.
The agent writes the wiki. The user curates the raw sources.

## WRITE-MODEL

```
Agent writes the wiki, human curates the raw sources.
- raw/**  →  READ-ONLY for the agent. NEVER write, edit, or delete.
- wiki/** →  Agent's working area. The agent actively maintains it.
- Every write to wiki/** MUST generate a log entry in wiki/log.md.
- Manual user edits to wiki pages → note in the next log entry.
```

## Three Layers

| Layer | Path | Owner |
|---|---|---|
| Immutable Sources | `raw/**` | User (additive only) |
| Agent Wiki | `wiki/**` | Agent (primary) |
| Schema | `CLAUDE.md` | Co-evolution |

## Three Operations

| Operation | Command | When |
|---|---|---|
| Ingest Source | `/ingest <path>` | New Material |
| Ask a Question | `/query <question>` | Ad-hoc |
| Health Check | `/lint` | Weekly |

## Commands

All commands are located under `.claude/commands/`. Reference documentation:

- `references/index-format.md` → Structure of `wiki/index.md`
- `references/log-format.md` → Structure of `wiki/log.md`
- `references/page-conventions.md` → Frontmatter rules, link style
- `references/karpathy-gist.md` → Verbatim reference to the original gist

## Bootstrapping

Upon first launch: Run `/bootstrap`. This creates the complete folder structure.
Idempotent — a second run will not overwrite existing files.

## Scale Limit

Optimized for ≤ 100–200 sources / ≤ 500 wiki pages.
Beyond this scale: Install `qmd` or migrate to GraphRAG. See `TRADE-OFFS.md`.