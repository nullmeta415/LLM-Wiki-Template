---
name: clip
description: Fetches content from a URL, converts it to Markdown, and saves it to raw/articles/<slug>.md. Can directly trigger /ingest immediately afterward. Entry point for web content.
allowed-tools:
  - Read
  - Write(raw/articles/**)
  - Write(wiki/log.md)
  - Bash
  - WebFetch
---

# /clip <url>

## WRITE-MODEL
The agent writes to the wiki; the human curates the raw sources.
- `/clip` writes to `raw/articles/` exactly ONCE — this is the only instance where the agent writes to `raw/`.
- After the clip operation, `raw/articles/<slug>.md` becomes READ-ONLY for the agent (just like all other files in `raw/`).
- No subsequent editing of the clipped file is permitted.

## Process

### Step 1: Fetch URL
Invoke the WebFetch tool on `<url>`. Use full-content mode.

### Step 2: Generate Slug
Derive from the page's `<title>` tag or H1 heading: use kebab-case, max. 60 characters.
Example: `andrej-karpathy-llm-wiki-gist`

### Step 3: Convert to Markdown
- HTML → Markdown (preserve headings, links, and code blocks).
- Remove navigation menus, footers, ads, and cookie banners.
- Images: Preserve alt text; use `![alt](raw/assets/<filename>)` as a placeholder.

### Step 4: Write Source Frontmatter
```yaml
---
type: raw-source
title: "<title>"
source_url: "<url>"
fetched_at: "YYYY-MM-DD HH:MM"
---
```

### Step 5: Save File
Write the file to `raw/articles/<slug>.md`.

### Step 6: Log Entry
```
## [YYYY-MM-DD HH:MM] clip |
``` <title>

- Saved: raw/articles/<slug>.md
- Source URL: <url>
- Size: ~N words
```

### Step 7: Ingest Offer
```
Clipped: raw/articles/<slug>.md (~N words)
Ingest directly? → /ingest raw/articles/<slug>.md
```

User decides. No automatic ingest.

## Error Handling

- URL unreachable: Output error message; do not write file.
- Paywall / Login wall: Notify user that manual placement in `raw/` is required.
- Very long content (>10,000 words): Output warning, but clip the full content regardless.