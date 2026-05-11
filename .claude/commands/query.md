---
name: query
description: Ask a question against the wiki. Reads relevant pages, synthesizes an answer with wiki-link citations. NEVER writes automatically — filing is a separate user decision.
allowed-tools:
    - Read
    - Write(wiki/**)
    - Glob
    - Grep
---

# /query <question>

## WRITE MODEL
The agent writes the wiki; the human curates the raw sources.
- `/query` is primarily a **read-only operation**.
- NEVER write automatically to `wiki/**`. Only upon explicit user command via `/file`.
- If a log entry is desired: Only after user confirmation.

## Process

### Step 1: Read Index
Read `wiki/index.md` in its entirety. Gain an overview of available pages.

### Step 2: Identify Relevant Pages
- Check index sections for relevance.
- Title Grep: `grep -r -i "<keyword>" wiki/` for keywords found in the question.
- Load a maximum of 8–10 pages per query (if more exist: prioritize based on relevance score).

### Step 3: Read Pages Fully
Read all identified pages completely—both frontmatter and body.

### Step 4: Synthesize Answer
- Provide an answer in natural language, structured clearly.
- **Cite every fact:** Place `[[page-name]]` directly after the statement.
- Link only to existing `wiki/` pages. Do not include external references in the answer.
- If a question cannot be answered: Explicitly state which pages are missing.

### Step 5: Offer to File
Always include the following at the end of the answer:
> "Save this answer as a new wiki page? → `/file <suggested-slug>`"

### Step 6: Log Entry (Optional)
Append a log entry only if the user explicitly confirms it, or if `/file` is executed.
Format:
```
## [YYYY-MM-DD HH:MM] query | <short-question>

- Pages consulted: [[page1]], [[page2]]
- Filed as: [[overviews/slug]] or "none"
```

## Constraints

- No external references or URLs in the query response.
- No hallucinations: If the wiki cannot answer a question, state so directly.
- No automatic writing to `wiki/**` without user decision.