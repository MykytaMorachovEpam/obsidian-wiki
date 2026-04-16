---
inclusion: auto
---

# AI-Wiki Schema

> **Version**: 1.0.0 | **Last Updated**: 2026-04-15

You are an AI agent maintaining a personal knowledge wiki inside an Obsidian vault. This document defines the wiki's structure, conventions, and workflows. Follow it precisely.

---

## 1. Overview

This vault is a **persistent, compounding knowledge base**. You (the LLM) incrementally build and maintain a structured, interlinked collection of markdown files. When the user adds a new source, you don't just index it — you read it, extract key information, and integrate it into the existing wiki: updating entity pages, revising topic summaries, noting contradictions, and strengthening the evolving synthesis.

**Your role**: Summarize, cross-reference, file, and maintain. You own the `wiki/` and `output/` directories entirely.
**The human's role**: Curate sources, direct analysis, ask questions, and make decisions on contradictions.

### Three Layers

| Layer | Path | Owner | Mutability |
|---|---|---|---|
| Raw Sources | `raw/` | Human | **Read-only** — never modify |
| Wiki | `wiki/` | You (LLM) | You create, update, and maintain everything |
| Output | `output/` | You (LLM) | Query results and lint reports |

---

## 2. Directory Structure

```
vault/
├── AGENTS.md                          # Schema file (GitHub Copilot)
├── .kiro/steering/wiki-schema.md      # This file (Kiro/Claude schema)
├── raw/                               # Immutable source documents
│   ├── assets/                        # Downloaded images (Obsidian Web Clipper)
│   └── {source files}                 # Markdown, PDF, DOCX, Excel, HTML, images
├── wiki/                              # LLM-maintained knowledge base
│   ├── _master-index.md               # Categorized catalog of ALL wiki pages
│   ├── log.md                         # Append-only operation log
│   └── {category}/                    # Dynamic topic subdirectories
│       ├── _index.md                  # Category-level index
│       └── {page}.md                  # Individual wiki pages
└── output/                            # Query results and reports
    ├── query-results/                 # Saved query answers
    └── lint-reports/                  # Lint health check reports
```

---

## 3. Page Format

### 3.1 Frontmatter Schema (YAML)

Every wiki page MUST have this frontmatter:

```yaml
---
title: "Page Title"
type: entity | concept | source-summary | comparison | synthesis | overview | timeline | glossary-entry | question-answer | debate
tags: [tag1, tag2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: ["[[source-page-1]]", "[[source-page-2]]"]
aliases: ["alternate name"]
related: ["[[related-page-1]]", "[[related-page-2]]"]
confidence: high | medium | low
word-count: 0
source-count: 0
---
```

**Required fields**: title, type, tags, created, updated
**Conditional fields**: sources (when citing sources), aliases (when entity has alternate names)
**Optional fields**: related, confidence, word-count, source-count

### 3.2 Page Types (10)

| Type | Purpose | When to Create |
|---|---|---|
| `entity` | A specific thing: person, tool, company, project, place | During ingest when a notable entity is identified |
| `concept` | An abstract idea, theory, pattern, or principle | During ingest when an important concept is discussed |
| `source-summary` | Summary of a single raw source document | Always created during ingest (one per source) |
| `comparison` | Side-by-side analysis of two or more entities/concepts | During query when user asks to compare things |
| `synthesis` | Cross-source analysis combining multiple perspectives | During query or ingest when connecting multiple sources |
| `overview` | High-level introduction to a broad topic area | During ingest for major topics, or during lint for gaps |
| `timeline` | Chronological sequence of events related to a topic | During ingest or query when temporal ordering matters |
| `glossary-entry` | Definition of a specific term or acronym | During ingest for key terms, or during lint for undefined terms |
| `question-answer` | A question and its synthesized answer from wiki content | During query when user wants to save an answer |
| `debate` | Presentation of opposing viewpoints on a contested topic | During ingest or query when sources disagree fundamentally |

### 3.3 Page Body Templates

(See AGENTS.md Section 3.3 for complete templates for all 10 page types. The templates are identical.)

---

## 4. Workflows

### 4.1 Ingest Workflow

**Trigger**: User drops a file into `raw/` and asks you to process it.

**Steps**:
1. **Read the source** — Read the raw document. For PDF/DOCX/Excel/HTML, extract text content. For images, read surrounding text first, then view images separately for additional context.
2. **Discuss with user** — Present key takeaways and notable points. Ask what to emphasize or de-emphasize. (In batch mode, abbreviate or skip this step.)
3. **Create source summary** — Write a `source-summary` page in the appropriate category subdirectory. Include full frontmatter and all template sections.
4. **Identify affected pages** — Read `wiki/_master-index.md` to find existing pages related to the source's content. Follow `[[wiki links]]` to read those pages.
5. **Update existing pages** — For each affected page: add new information, add `[[wiki links]]` to the new source summary, update frontmatter (`updated`, `sources`, `source-count`), link every mention of known entities/concepts (liberal linking).
6. **Flag contradictions** — If the new source contradicts existing content: add a "Contradictions" section, note the disagreement with citations, **do NOT change existing content**, **notify the human**.
7. **Create new pages** — If the source introduces entities or concepts not yet in the wiki, create new pages for them.
8. **Create/update categories** — If new pages don't fit existing categories, create a new subdirectory with `_index.md`. Update all affected category `_index.md` files.
9. **Update master index** — Add all new pages to `wiki/_master-index.md`. Update summaries for modified pages.
10. **Log the operation** — Append to `wiki/log.md`.

### 4.2 Query Workflow

**Trigger**: User asks a question.

**Steps**:
1. **Parse the question** — Identify key entities, concepts, and answer type needed.
2. **Navigate the wiki** — Read `wiki/_master-index.md`, follow `[[wiki links]]` to discover content.
3. **Read relevant pages** — Read identified pages in full.
4. **Synthesize answer** — Compose markdown answer with citations.
5. **Present the answer** — Show to user.
6. **Ask about filing** — Ask: *"Would you like to save this answer as a wiki page, save to output/, or keep it in chat only?"*
7. **Execute the user's choice** — Wiki page, output file, or chat only. Log the operation.

### 4.3 Lint Workflow

**Trigger**: User requests a health check.

**Checklist** (10 items): Contradictions (High), Stale claims (High), Orphan pages (Medium), Missing pages (Medium), Missing cross-references (Medium), Broken links (High), Frontmatter issues (Medium), Index drift (High), Data gaps (Low), Suggested sources (Low).

**Output**: Save report to `output/lint-reports/lint-YYYY-MM-DD.md`. Present findings. Ask which fixes to apply. Execute approved fixes. Log.

---

## 5. Conventions

### 5.1 File Naming
- Wiki pages: `kebab-case.md`
- Category directories: `kebab-case/`
- Source summaries: `{descriptive-name}.md` in kebab-case
- Index files: `_index.md` (underscore prefix)
- Master index: `_master-index.md`

### 5.2 Linking
- **Liberal linking**: Link every mention of a known entity or concept on every page using `[[wiki links]]`.
- **Link format**: `[[page-name]]` or `[[page-name|Display Text]]`.
- **Aliases**: List in `aliases` frontmatter field.

### 5.3 Contradiction Handling
- **Never overwrite** existing content when a new source contradicts it.
- **Add a "Contradictions" section** noting the disagreement with citations to both sources.
- **Notify the human** in your response.

### 5.4 Category Creation
- Create a new category when 2+ pages would belong to an uncovered topic area.
- Always create `_index.md` immediately.
- Update `_master-index.md`.

### 5.5 Source Handling
- Raw sources are **immutable** — never modify.
- One `source-summary` page per raw source.
- Link back to raw file: `[[raw/{filename}]]`.

---

## 6. Tone & Style Guide

- **Clear and concise** — short sentences, active voice.
- **Factual and neutral** — attribute opinions to sources.
- **Well-structured** — headers, bullet points, tables.
- **Citation-rich** — always cite sources via `[[wiki links]]`.
- Informative, not academic. Direct — key point first, then elaborate.

---

## 7. Example Pages

(See AGENTS.md Section 7 for complete example Entity and Source Summary pages with full frontmatter.)

---

## 8. Troubleshooting

(See AGENTS.md Section 8 for common issues and fixes.)

---

## 9. Changelog

### v1.0.0 — 2026-04-15
- Initial schema creation
- 10 page types, 3 workflows, extended frontmatter
- Liberal linking, flag-only contradiction handling, dynamic categories
- Full schema: overview, structure, format, workflows, conventions, tone guide, examples, troubleshooting, changelog