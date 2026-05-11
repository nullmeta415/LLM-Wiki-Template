# SETUP.md — Installation & Activation

> Full reference: [README.md](./README.md) | Workflow: [WORKFLOW.md](./WORKFLOW.md) | Scale limits: [TRADE-OFFS.md](./TRADE-OFFS.md)

## Requirements

- **Obsidian** ≥ v1.12
- **Claude Code** (current) — or Cowork (equivalent for file operations)
- **Git** (strongly recommended for versioning)
- Optional: `qmd` (`pip install qmd`) for better search performance beyond ~200 pages

## Step 1: Clone the template

```bash
git clone https://github.com/Whitefox75/LLM-Wiki-Template.git my-wiki
cd my-wiki
```

**IMPORTANT:** This workspace is a standalone folder.
Do **not** place it inside an existing personal Obsidian vault.

> Reason (Steph Ango, Obsidian Co-Creator): Keep your personal vault clean.
> Let the agent work in the "messy vault". Only manually carry over curated artifacts.

## Step 2: Start the agent and bootstrap

**Claude Code:**
```bash
claude
/bootstrap
```

**Cowork (Claude Desktop):**
Open the folder in Cowork, then in the chat:
```
/bootstrap
```

The agent creates the full folder structure and outputs a report.

## Step 3: Configure Obsidian

1. Open Obsidian → **Open folder as vault** → select this workspace folder.
2. **Attachment folder:** Settings → Files and links → "Attachment folder path" = `raw/assets/`
3. **Attachment hotkey:** Settings → Hotkeys → "Download attachments for current file" → `Ctrl+Shift+D`

### Recommended plugins

| Plugin | Purpose | Priority |
|---|---|---|
| **Obsidian Web Clipper** | Browser extension for the `/clip` workflow | High |
| **Dataview** | Query pages via frontmatter fields | Optional |
| **Marp** | Render wiki pages as slides | Optional |

Install Web Clipper: [obsidian.md/clipper](https://obsidian.md/clipper) (browser extension)

## Step 4: Test with your first source

**Option A — Clip a URL:**
```
/clip https://example.com/interesting-article
/ingest raw/articles/<slug>.md
```

**Option B — Drop a file manually:**
```bash
cp ~/Downloads/paper.pdf raw/papers/paper.pdf
```
```
/ingest raw/papers/paper.pdf
```

**Option C — Import existing Markdown notes:**
```bash
cp ~/my-vault/note.md raw/articles/note.md
```
```
/ingest raw/articles/note.md
```

## Step 5: Watch Obsidian in parallel

Keep Obsidian open during ingestion. In Graph View (`Ctrl+G`) watch new Entity and Concept nodes appear and get linked in real time.

## Optional: qmd for fast search

Beyond ~200 pages, `qmd` is worth installing for semantic search:

```bash
pip install qmd
qmd index wiki/
```

After that, `/search` uses `qmd` automatically instead of grep.

## Recommended Git workflow

```bash
# After every ingest
git add wiki/
git commit -m "ingest: <source-title>"

# After lint fixes
git add wiki/
git commit -m "lint: fix orphans / contradictions"
```

Protects against silently drifting content (agent-drift). Without Git, agent-drift is undetectable — see [TRADE-OFFS.md](./TRADE-OFFS.md).

## Next steps

After setup:
- Understand the workflow cadence → [WORKFLOW.md](./WORKFLOW.md)
- Know the scale limits → [TRADE-OFFS.md](./TRADE-OFFS.md)
- Run `/lint` after the first dozen ingests

---