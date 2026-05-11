---

name: reindex
description: Rebuilds wiki/index.md from scratch by scanning all files in wiki/. Backup performed beforehand. Deterministic — the same wiki state generates the same index.

allowed-tools:
  - Read
  - Write(wiki/index.md)
  - Write(wiki/index.md.bak)
  - Write(wiki/log.md)
  - Glob
  - Bash
---

# /reindex

## WRITE-MODEL

An agent writes the wiki, a human curates the raw sources.

- Writes ONLY to `wiki/index.md`, `wiki/index.md.bak`, and `wiki/log.md`.

- `raw/**` remains untouched.


## Procedure

### Step 1: Backup

```bash
cp wiki/index.md wiki/index.md.bak
```
If `wiki/index.md` does not exist: skip.

### Step 2: Scan Wiki Files
```bash
find wiki/ -name "*.md" -not -name "index.md" -not -name "log.md" -not -path "wiki/lint-reports/*"
```

For each file found:
- Read the front matter (type, title/name, domain)
- Extract the short description (first non-blank line after the first H2)

### Step 3: Build the Index

Structure:
```markdown
---
type: index
last_updated: "YYYY-MM-DD"
total_sources: N
total_entities: N
total_concepts: N
total_comparisons: N
total_overviews: N
---

# Wiki Index

_Last updated: YYYY-MM-DD — rebuilt via /reindex_
_Auto-maintained by Agent. Use `/reindex` to rebuild._

---

## Sources (N)
- [[sources/slug]] — Short description

## Entities (N)
- [[entities/Name]] — One-liner

## Concepts (N)
- [[concepts/name]] — One-liner

## Comparisons (N)
- [[comparisons/a-vs-b]] — Criteria: X, Y

## Overviews (N)
- [[overviews/domain]] — Scope
```

Sorting within each section: alphabetically by slug/name.

### Step 4: Write Index
Completely overwrite `wiki/index.md` with the newly built index.

### Step 5: Log Entry
```
## [YYYY-MM-DD HH:MM] reindex | Full rebuild

- Sources: N
- Entities: N
- Concepts: N
- Comparisons: N
- Overviews: N
- Backup: wiki/index.md.bak
```

### Step 6: Report to User
- Counts per category
- Note: Backup located at `wiki/index.md.bak`
- If discrepancies compared to the old index are detected: list them (new pages, missing entries)