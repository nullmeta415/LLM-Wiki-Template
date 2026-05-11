# LLM Wiki Template

> A Karpathy-style persistent knowledge artifact — compiled once, kept current, machine-readable by design.
>
> Inspired by [Andrej Karpathy's llm-wiki.md](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) (April 2026)

---

## Why not RAG?

| | Traditional RAG | LLM Wiki |
|---|---|---|
| **Per query** | Chunk → embed → retrieve → reconstruct | Read compiled wiki → answer with citations |
| **Knowledge state** | Stateless — rebuilt every time | Stateful — grows with every ingestion |
| **Contradictions** | Silent | Detected and flagged explicitly |
| **Citations** | Approximate ("based on chunk 3") | Exact (`[[sources/slug]]` wikilinks) |
| **Human control** | Opaque retrieval pipeline | Human-in-the-loop at every write operation |

The core idea: **treat your knowledge base like a codebase.** The agent is the programmer, the wiki is the compiled output, raw sources are immutable inputs.

```
Obsidian = IDE
You       = Architect
Agent     = Programmer
Wiki      = Codebase
```

---

## How it works

```
raw/              ← Your immutable source documents (PDFs, notes, clipped URLs)
wiki/             ← Agent-maintained knowledge base (Sources, Entities, Concepts, ...)
_page-templates/  ← Frontmatter skeletons per page type
tools/            ← Utility scripts
.claude/          ← Claude Code commands & agent schema (CLAUDE.md)
```

**One ingestion → 10–15 wiki pages touched.** Each source fans out into Entity pages (people, tools, companies), Concept pages (topics, methods), and a Source summary — all cross-linked.

### Operations

| Command | What it does | When |
|---|---|---|
| `/bootstrap` | Generates the full folder structure | Once, on setup |
| `/ingest <path\|url>` | Processes a source into the wiki | Ad-hoc |
| `/clip <url>` | Clips a URL to `raw/` before ingestion | Ad-hoc |
| `/query <question>` | Synthesizes answers from wiki with citations | Ad-hoc |
| `/file <slug>` | Saves a query result as a permanent wiki page | Weekly |
| `/lint` | Reports contradictions, orphans, stubs, broken links | Monthly |
| `/search <term>` | Full-text search across the wiki | Ad-hoc |
| `/reindex` | Rebuilds `wiki/index.md` | After bulk changes |

### Human-in-the-loop by design

The agent **never** writes to `raw/`, never auto-fixes lint issues, and never files query results without explicit instruction. Every write decision is yours.

---

## Real-world example

This template was built alongside a practical use case:

- **Input:** A personal Obsidian vault for IT vocational training (networking, OS, security, project management, IT law)
- **Output:** A structured LLM Wiki — machine-readable, citation-linked, contradiction-aware
- **Result:** The same knowledge base serves both human review (Obsidian graph) and agent queries (structured wiki traversal) without duplication

The key insight: **your human-curated notes become structured agent input.** The wiki is not a copy of your notes — it is a compiled, normalized, cross-linked representation optimized for LLM consumption.

---

## Quickstart

### Requirements

- [Obsidian](https://obsidian.md/) v1.12+
- [Claude Code](https://claude.ai/claude-code) (current version)
- Git (recommended)
- Optional: `qmd` for full-text search performance at scale (>200 sources)

### Setup

```bash
# 1. Clone the template
git clone https://github.com/Whitefox75/LLM-Wiki-Template.git my-wiki
cd my-wiki

# 2. Open the folder in Obsidian as a new vault

# 3. In Claude Code, run:
/bootstrap
```

Then drop a source file into `raw/` and run `/ingest <filename>`. Watch the wiki grow.

Full setup guide: [SETUP.md](./SETUP.md) | Workflow reference: [WORKFLOW.md](./WORKFLOW.md) | Scale limits: [TRADE-OFFS.md](./TRADE-OFFS.md)

---

## Scale

Optimized for **≤ 200 sources / ≤ 500 pages**. Beyond that, index-based retrieval slows down — see [TRADE-OFFS.md](./TRADE-OFFS.md) for migration paths (qmd, GraphRAG).

---

## License

MIT

---