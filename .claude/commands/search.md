---
name: search
description: Full-text search within the wiki. Uses `qmd` if available; otherwise, falls back to `grep`. Read-only — no log entry.
allowed-tools:
    - Read
    - Glob
    - Grep
    - Bash
---

# /search <query>

## WRITE-MODEL
Read-only operation. No log entry. No writes to `wiki/**`.

## Workflow

### Step 1: `qmd` Check
```bash
which qmd 2>/dev/null && echo "qmd available" || echo "grep fallback"
```

### Step 2a: `qmd` Available
```bash
qmd search "<query>" wiki/
```
Return results in a formatted manner: path, snippet, score.

### Step 2b: `grep` Fallback
```bash
grep -r -i -n "<query>" wiki/ --include="*.md" | head -30
```
Plus Index Title Match:
```bash
grep -i "<query>" wiki/index.md
```

### Step 3: Output Results
- Relevant pages accompanied by a brief contextual snippet.
- Direct pointers to the most useful pages.
- Suggestion: "Shall I initiate `/query` with a specific question based on these pages?"

## Constraints

- No writing.
- No log entry.
- In the event of empty results: explicitly state which search terms yielded no matches.