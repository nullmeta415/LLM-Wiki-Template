---
name: ingest
description: Incorporating a new source into the wiki. Generates a Source Page, updates Entity and Concept Pages, and updates the Index and Log. The central command of the LLM Wiki Pattern.
allowed-tools:
    - Read
    - Write(wiki/**)
    - Glob
    - Grep
    - Bash
---

# /ingest <path-or-url>

## WRITE-MODEL
The agent writes the wiki; the human curates the raw sources.
- `raw/**` is READ-ONLY. NEVER write to, edit, or delete files here.
- `wiki/**` is the working area. All outputs go here.
- Every write operation MUST generate a log entry in `wiki/log.md`.

## Process

### Step 1: Read Source
- Local Path: Read the file located in `raw/`.
- URL: Issue a prompt indicating that `/clip <url>` should be used first. Then, wait for the local path to be provided.
- If the path is not located within `raw/`: Issue a warning. Read the source anyway, but document this exception.

### Step 2: Ask User for Clarification (2–3 questions)
Ask these questions **before** you start writing:
1. "What specific focus should I prioritize during the ingestion process?"
2. "Are there any specific Entities or Concepts that are particularly important?"
3. If unclear: "In which language should the wiki pages be written? (The source's language, or another?)"

Wait for the user's response before proceeding.

### Step 3: Write Source Page
Path: `wiki/sources/<slug>.md` (Slug: kebab-case version of the title)
Template: `_page-templates/source.md`

Content:
- **Summary:** 200–500 words. A concise distillation of the content.
- **Key Claims:** Bullet points. Each claim links back to `[[sources/this-slug]]`.
- **Notable Quotes:** Max. 1 quote, ≤15 words. - **Open Questions:** Unsubstantiated aspects.
- **Related Pages:** Links to all generated/touched pages.

### Step 4: Update/Create Entity Pages
For every mentioned person / company / product / tool:
- Existing Page: Update the relevant section; add the new source to the `sources:` frontmatter.
- New Entity: Create a page using the `_page-templates/entity.md` template.
- In case of a naming conflict: Apply the suffix convention (e.g., `Apex-Software`, `Apex-Hardware`).

### Step 5: Update/Create Concept Pages
Proceed analogously to Step 4 for topics / ideas / methods / theorems.

### Step 6: Contradiction Check
Verify new claims against existing wiki pages:
- If a contradiction is found: Add a `## Contradictions` section to the affected page.
- Format: `[[sources/new-source]] claims X; [[sources/old-source]] claims Y.`
- Note this under `## Open Questions` on the source page.

### Step 7: Update Index
`wiki/index.md`: Insert the new pages into the appropriate section.
Format: `- [[sources/slug]] — Short description`

### Step 8: Append Log Entry
```
## [YYYY-MM-DD HH:MM] ingest | <source-title>

- Pages created: [[sources/slug]], [[entities/X]], [[concepts/y]]
- Pages updated: [[entities/Z]]
- Contradictions found: [[sources/a]] ↔ [[sources/b]] on claim X
- Notes: <focus/language/special notes>
```

### Step 9: Diff Report to User
Output:
- Newly created pages (with paths)
- Updated pages
- Contradictions found
- Recommendation: `git commit -m "ingest: <title>"`