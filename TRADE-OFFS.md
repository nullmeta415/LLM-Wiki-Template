# TRADE-OFFS.md — Scale Limits & Known Weaknesses

> Full reference: [README.md](./README.md) | Setup: [SETUP.md](./SETUP.md) | Workflow: [WORKFLOW.md](./WORKFLOW.md)

## LLM Wiki vs. traditional RAG

| Dimension | Traditional RAG | LLM Wiki |
|---|---|---|
| **Knowledge state** | Stateless — rebuilt every time | Stateful — grows with every ingestion |
| **Contradictions** | Silent | Explicitly detected and flagged |
| **Citations** | Approximate ("chunk 3") | Exact (`[[sources/slug]]`) |
| **Human control** | Opaque retrieval pipeline | Human-in-the-loop at every write operation |
| **Scale** | Unlimited (vector DB) | ≤ 200 sources / ≤ 500 pages |
| **Setup effort** | High (embedding, vector DB, pipeline) | Low (folder + agent) |

## Scale Limits

| Limit | Threshold | Consequence / Mitigation |
|---|---|---|
| **Page count** | > 200 sources / > 500 pages | Index-based retrieval slows down. Mitigation: install `qmd` or migrate to GraphRAG. |
| **Contradiction detection** | — | The LLM misses semantically contradictory claims phrased differently. Mitigation: manual lint reviews, run `/lint` regularly. |
| **Ingest cost** | Token-intensive | Every ingest touches 10–15 pages, each read and written in full. Real monetary cost in API-based workflows. |
| **Agent drift** | Rewrite loops | Without Git versioning, silent content drift accumulates over time. Mitigation: `git commit` after every ingest. |
| **Entity disambiguation** | — | Two same-named entities (e.g. two companies named "Apex") get merged. Mitigation: slug suffix convention (`Apex-Software`, `Apex-Hardware`). |
| **Non-textual sources** | Images / audio | Agent cannot fully read Markdown + referenced images in a single pass. Mitigation: ingest text first, then request referenced images separately. |
| **Classification ambiguity** | Concept vs. entity | The boundary is sometimes blurry (e.g. "Transformer" = concept or entity?). Mitigation: when in doubt, use Entity + `aliases` field. |

## Critical perspectives

**Mehul Gupta** and others have publicly noted that the approach hits its limits at large scale (>500 pages, >50 simultaneously active domains) — the context-window overhead for `/ingest` grows non-linearly.

**Karpathy's own position:** This is a **pattern, not a product**. It does not scale to enterprise RAG systems, but is highly effective for personal knowledge curation within a clearly bounded domain ("my learning focus for the next year").

## When NOT to use this system

- More than 3 mutually independent knowledge domains simultaneously
- Sources arriving faster than you can ingest them (news-feed mode)
- Team workflows with multiple simultaneous authors
- Requirement for semantic search without `qmd` and with >200 pages
- When source data is primarily non-textual (audio, video, images)

## Migration paths

| Situation | Recommendation |
|---|---|
| > 200 sources, search is slow | Install `qmd` (`pip install qmd && qmd index wiki/`) |
| > 500 pages, multiple domains | Separate workspace instances per domain |
| > 1000 pages | GraphRAG or LlamaIndex with vector DB |
| Team context | GraphRAG or Notion-AI-based approach |
| Production RAG | LlamaIndex / LangChain with vector DB |

---