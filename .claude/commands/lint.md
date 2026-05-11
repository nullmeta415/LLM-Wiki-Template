---
name: lint
description: Periodic health check of the entire wiki. Identifies contradictions, orphans, stubs, missing concepts, stale claims, broken links, and index drift. No automatic fixes.
allowed-tools:
    - Read
    - Write(wiki/lint-reports/**)
    - Write(wiki/log.md)
    - Glob
    - Grep
    - Bash
---

# /lint

## WRITE MODEL
Agent writes to the wiki; humans curate the raw source files.
- `/lint` writes ONLY to `wiki/lint-reports/` and `wiki/log.md`.
- No automatic fixes are applied to other wiki pages.
- The user decides, on an item-by-item basis, what action to take.

## Process

Scan `wiki/` in its entirety. Generate a report in `wiki/lint-reports/YYYY-MM-DD.md`.

### Check 1: Contradictions
```bash
grep -r "## Contradictions" wiki/ --include="*.md" -l
```
For each page found: Read the content of the relevant section and document it in the report.

### Check 2: Orphans
For every page in `wiki/` (excluding `index.md` and `log.md`):
```bash
grep -r "[[PageName]]" wiki/ --include="*.md" | grep -v "^wiki/index.md"
```
Pages with no inbound links → Orphan.

### Check 3: Stubs
For every page: Count the words in the body text (excluding frontmatter and headings).
Pages with < 50 words → Stub.

### Check 4: Missing Concepts
Extract all terms that appear in ≥3 pages but do not have their own dedicated `wiki/concepts/` page.
```bash
# Pseudo-logic: grep for frequent terms, cross-reference against wiki/concepts/
```

### Check 5: Stale Claims
Pages that lack `evergreen: true` in their frontmatter, and whose linked sources are older than 180 days. Extract date from the `ingested_at` frontmatter of source pages.

### Check 6: Broken Links
Extract all `[[...]]` links within `wiki/`. Verify that the target file exists.
```bash
grep -ro "\[\[.*\]\]" wiki/ | sed 's/.*\[\[//;s/\]\].*//'
```

### Check 7: Index Drift
- Pages in `wiki/` but not in `wiki/index.md` → missing entries.
- Entries in `wiki/index.md` but no corresponding file → dead links.

## Report Format

```markdown
# Lint Report — YYYY-MM-DD

## Summary
- Contradictions: N
- Orphans: N
- Stubs: N
- Missing Concepts: N
- Stale Claims: N
- Broken Links: N
- Index Drift: N

## Contradictions
- [[page]] ↔ [[page2]] — Claim X

## Orphans
- [[entities/X]] — no inbound links

## Stubs
- [[concepts/y]] — 23 words

## Missing Concepts
- "Term" — mentioned in [[p1]], [[p2]], [[p3]]

## Stale Claims
- [[sources/old-source]] — ingested 2025-06-01, not evergreen

## Broken Links
- [[entities/ghost]] — referenced in [[sources/x]], file does not exist

## Index Drift
- In wiki/, not in index: [[concepts/z]]
- In index, no file: [[entities/deleted]]

## Recommended Actions
- Fix contradiction in [[page]]: re-ingest [[sources/a]] with a new focus
- Delete orphan [[entities/X]] or link to it
- Expand stub [[concepts/y]]
- Create concept page for "Term"
```

## Log Entry

```
## [YYYY-MM-DD HH:MM] lint | <N> issues found

- Report: [[lint-reports/YYYY-MM-DD]]
- Critical: <Contradictions-Count> contradictions, <Broken-Links-Count> broken links
```