---
name: bootstrap
description: One-time setup of the LLM Wiki Workspace. Creates the complete folder structure and generates seed files. Idempotent — existing files are never overwritten.
allowed-tools:
   - Read
   - Write(wiki/**)
   - Write(raw/**)
   - Write(_page-templates/**)
   - Bash
   - Glob
---

# /bootstrap

## WRITE-MODEL
The Agent writes the wiki; the human curates the raw sources.
- `raw/**` is READ-ONLY for the Agent after bootstrapping. NEVER write to, edit, or delete files in this directory subsequently.
- `wiki/**` is the Agent's working area.
- Every write operation within `wiki/**` MUST generate a log entry in `wiki/log.md`.

## Process

1. Check if the workspace has already been initialized (i.e., if `wiki/log.md` exists). If so: print "Workspace already initialized. No overwriting." and stop.

2. Create the following folder structure (only if it does not already exist):

```
raw/articles/
raw/papers/
raw/transcripts/
raw/docs/
raw/assets/
wiki/sources/
wiki/entities/
wiki/concepts/
wiki/comparisons/
wiki/overviews/
wiki/lint-reports/
_page-templates/
tools/
.claude/commands/
.claude/skills/llm-wiki/references/
```

3. Create `wiki/index.md` (seed file) if it does not already exist.
4. Create `wiki/log.md` (seed file containing a format example) if it does not already exist.
5. Create `.gitignore` if it does not already exist:
   ```
   .DS_Store
   *.pyc
   tools/*.egg-info/
   ```
6. Create `tools/.gitkeep` if it does not already exist.

7. Append a log entry to `wiki/log.md`:
   ```
   ## [YYYY-MM-DD HH:MM] bootstrap | Workspace initialized

   - Folders created: raw/, wiki/, _page-templates/, tools/
   - Seed files: wiki/index.md, wiki/log.md
   ```

8. Output Report: A list of all created folders and files, along with a hint regarding the next step (`/clip <url>` or place files manually in `raw/`, then `/ingest`).

## Idempotency Rule

Existing files or folders are **never** overwritten or cleared. A second run is safe.