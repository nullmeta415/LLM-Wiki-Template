---
name: file
description: Saves the preceding agent response (typically a /query response) as a new wiki page. Updates the index and log. Executed only upon explicit user command.
allowed-tools:
    - Read
    - Write(wiki/**)
    - Glob
---

# /file <slug>

## WRITE-MODEL
The agent writes the wiki; the human curates the raw sources.
- This command is executed ONLY upon explicit user command.
- NEVER file automatically after a `/query` — the user must explicitly invoke `/file`.
- `raw/**` remains untouched.

## Workflow

### Step 1: Determine Content
- Default: The immediately preceding agent response (typically a `/query` response).
- If the user explicitly specifies content: use that content.

### Step 2: Determine Page Type
Based on the content:
- Synthesis regarding a Domain → `wiki/overviews/<slug>.md`
- Comparison of two things → `wiki/comparisons/<slug>.md`
- Response regarding a Concept → `wiki/concepts/<slug>.md`
- Response regarding an Entity → `wiki/entities/<slug>.md`

If unsure: Ask the user or use the default `wiki/overviews/<slug>.md`.

### Step 3: Write Page
- Load the appropriate template from `_page-templates/`.
- Fit the content into the template structure.
- Fully populate the frontmatter (type, created, sources derived from the `/query` citations).
- Retain all `[[citations]]` from the query response.

### Step 4: Update Index
Add the new page to the appropriate section of `wiki/index.md`.

### Step 5: Log Entry
```
## [YYYY-MM-DD HH:MM] file |
``` <slug>

- Filed as: [[<type>/<slug>]]
- Type: <page-type>
- Source query: "<original question>"
- Citations preserved: [[p1]], [[p2]]
```

- Step 6: Confirmation to User
- Path to the new page
- Note: The page can now serve as a source for future queries.