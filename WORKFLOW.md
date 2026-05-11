# WORKFLOW.md — Operations & Cadence

> Full reference: [README.md](./README.md) | Setup: [SETUP.md](./SETUP.md) | Scale limits: [TRADE-OFFS.md](./TRADE-OFFS.md)

## Recommended cadence

| Cadence | Operation | Duration | Trigger |
|---|---|---|---|
| Per source | `/clip` → `/ingest` | 5–15 min | New interesting material |
| Ad-hoc | `/query <question>` | 1–5 min | A question comes up |
| Ad-hoc | `/search <term>` | < 1 min | Quick full-text lookup |
| Ad-hoc | `/file <slug>` | < 1 min | Query answer is worth keeping |
| Weekly | `/lint` | 10–20 min | e.g. every Sunday |
| Monthly | `/reindex` | 2 min | After large ingest batches |

## The compounding loop

```mermaid
flowchart LR
  A[Raw Source] --> B["/ingest"]
  B --> C[Source Page]
  B --> D[Entity Pages updated]
  B --> E[Concept Pages updated]
  B --> F[Index updated]
  B --> G[Log Entry]

  H["/query"] --> I[Answer with citations]
  I --> J["/file"]
  J --> C

  K["/lint"] --> L[Contradictions / Orphans / Stubs]
  L --> M[User decides fixes]
  M --> B
```

## Ingest flow in detail

```
User: /clip https://...
  → Agent fetches URL → saves to raw/articles/<slug>.md

User: /ingest raw/articles/<slug>.md
  → Agent asks 2–3 clarifying questions
  → User sets focus
  → Agent writes Source page
  → Agent updates Entity and Concept pages
  → Agent updates index
  → Agent appends log entry
  → Agent reports diff to user
```

## Query flow

```
User: /query "What does the wiki say about X?"
  → Agent reads index.md
  → Agent identifies relevant pages
  → Agent reads those pages in full
  → Agent synthesizes answer with [[citations]]
  → Agent offers /file

User (optional): /file x-overview
  → Agent saves answer as wiki/overviews/x-overview.md
  → Index + log update
```

## Lint cycle

```
User: /lint
  → Agent scans all of wiki/
  → Agent creates wiki/lint-reports/YYYY-MM-DD.md
  → Agent reports: Contradictions / Orphans / Stubs / Missing Concepts / Stale Claims / Broken Links / Index Drift
  → User decides per item
  → User triggers necessary ingest/edit actions
```

## Human-in-the-loop principle

**The user (Architect):**
- Curates which sources land in `raw/`
- Sets focus and priorities during ingest
- Decides whether query answers get filed
- Reviews lint reports and decides on fixes
- Can intervene and correct wiki pages at any time (with log entry)

**The agent (Programmer):**
- Writes the wiki
- Maintains index and log
- Detects contradictions and gaps
- **Never** writes to `raw/`
- **Never** auto-fixes lint issues

## Compounding effect

The system accumulates value over time. After 50 ingests, Entity and Concept pages are already so densely linked that new sources primarily extend existing pages rather than creating new ones. The marginal effort per source decreases — the quality of answers increases.

This effect **does not occur** with:
- Inconsistent cadence (long gaps → index drift)
- Missing Git commits (agent drift goes unnoticed)
- Skipped lint cycles (contradictions accumulate)

---