# AI-Wiki

A personal knowledge base maintained by an LLM agent inside an [Obsidian](https://obsidian.md) vault. Instead of retrieving from raw documents on every question (like traditional RAG), the LLM incrementally builds and maintains a structured, interlinked wiki that compounds knowledge over time.

## How It Works

The vault has three layers:

| Layer | Path | Owner | Purpose |
|---|---|---|---|
| **Raw Sources** | `vault/raw/` | You | Drop in articles, PDFs, spreadsheets, images. Immutable — the LLM reads but never modifies. |
| **Wiki** | `vault/wiki/` | LLM | Structured markdown pages: summaries, entity pages, concept pages, comparisons, syntheses. Fully LLM-managed. |
| **Output** | `vault/output/` | LLM | Query results and lint reports that aren't filed into the wiki. |

## Three Workflows

1. **Ingest** — Drop a source into `raw/`, tell the LLM to process it. It reads the source, creates a summary page, builds entity/concept pages, cross-links everything, and updates the master index.
2. **Query** — Ask a question. The LLM navigates the wiki graph, synthesizes an answer with citations, and optionally files it back as a new wiki page.
3. **Lint** — Request a health check. The LLM scans for broken links, orphan pages, contradictions, stale claims, missing cross-references, and data gaps.

## Getting Started

### Prerequisites
- [Obsidian](https://obsidian.md) (for browsing the wiki and graph view)
- An LLM-powered IDE with file access — [Claude Code](https://claude.com/claude-code), [Kiro](https://kiro.dev), GitHub Copilot, Cursor, or similar

### Setup

1. Clone this repo
2. Open the `vault/` folder as an Obsidian vault
3. Open the **repo root** in your LLM-powered IDE (Claude Code, Kiro, etc.)
4. Add source documents to `vault/raw/`
5. Tell the LLM: *"Ingest the new file in vault/raw/"* (Claude Code: `/ingest <file>`)
6. Browse the generated wiki in Obsidian — follow links, explore the graph view

### Agent Configuration

The canonical schema lives at the repo root in `AGENTS.md`. Each supported agent picks it up via its own entry point:

| Agent | Entry point | How it loads AGENTS.md |
|---|---|---|
| Claude Code | `CLAUDE.md` | `@AGENTS.md` import |
| Kiro | `.kiro/steering/wiki-schema.md` | Delegates sections to AGENTS.md; `inclusion: auto` |
| GitHub Copilot / generic | `AGENTS.md` | Read directly |

`AGENTS.md` defines the page types, frontmatter schema, workflow steps, linking conventions, and style guide.

**Claude Code also ships with:**
- `.claude/settings.json` — permissions enforcing layer ownership (`vault/raw/` read-only; `vault/wiki/` and `vault/output/` writable)
- `.claude/commands/` — slash commands `/ingest`, `/query`, `/lint` (thin pointers to AGENTS.md §4.1–§4.3)

## Repository Structure

```
.
├── README.md
├── AGENTS.md                      # Canonical LLM schema (single source of truth)
├── CLAUDE.md                      # Claude Code entry point (imports AGENTS.md)
├── .claude/                       # Claude Code configuration
│   ├── settings.json              # Permissions (raw/ read-only, wiki/output writable)
│   └── commands/                  # /ingest, /query, /lint slash commands
├── .kiro/steering/                # Kiro entry point (references AGENTS.md)
├── .gitignore
└── vault/
    ├── .obsidian/                 # Obsidian config
    ├── raw/                       # Source documents (gitignored)
    ├── wiki/                      # LLM-generated wiki (gitignored)
    └── output/                    # Query results & reports (gitignored)
```

`vault/raw/`, `vault/wiki/`, and `vault/output/` are gitignored — the wiki is generated from your sources and can be rebuilt by re-ingesting them.

## Wiki Schema

The wiki supports 10 page types:

| Type | Purpose |
|---|---|
| `entity` | A specific thing: person, tool, company, project |
| `concept` | An abstract idea, theory, pattern, or principle |
| `source-summary` | Summary of a single raw source document |
| `comparison` | Side-by-side analysis of entities or concepts |
| `synthesis` | Cross-source analysis combining multiple perspectives |
| `overview` | High-level introduction to a broad topic area |
| `timeline` | Chronological sequence of events |
| `glossary-entry` | Definition of a term or acronym |
| `question-answer` | A question and its synthesized answer |
| `debate` | Opposing viewpoints on a contested topic |

See `AGENTS.md` for the full schema, templates, and conventions.

## License

Private repository. Not licensed for redistribution.
