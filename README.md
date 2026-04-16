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
- An LLM-powered IDE with file access — [Kiro](https://kiro.dev), Cursor, or similar

### Setup

1. Clone this repo
2. Open the `vault/` folder as an Obsidian vault
3. Add source documents to `vault/raw/`
4. Tell the LLM: *"Ingest the new file in raw/"*
5. Browse the generated wiki in Obsidian — follow links, explore the graph view

### Agent Configuration

The LLM schema is defined in two places (identical content, different IDE conventions):

- `vault/AGENTS.md` — for GitHub Copilot and general agents
- `vault/.kiro/steering/wiki-schema.md` — for Kiro IDE

These files define the page types, frontmatter schema, workflow steps, linking conventions, and style guide the LLM follows.

## Repository Structure

```
.
├── README.md
├── .gitignore
└── vault/
    ├── AGENTS.md                  # LLM agent schema
    ├── .kiro/steering/            # Kiro-specific steering
    ├── .obsidian/                 # Obsidian config
    ├── raw/                       # Source documents (gitignored)
    ├── wiki/                      # LLM-generated wiki (gitignored)
    └── output/                    # Query results & reports (gitignored)
```

`raw/`, `wiki/`, and `output/` are gitignored — the wiki is generated from your sources and can be rebuilt by re-ingesting them.

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

See `vault/AGENTS.md` for the full schema, templates, and conventions.

## License

Private repository. Not licensed for redistribution.
