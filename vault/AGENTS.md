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
├── AGENTS.md                          # This file (GitHub Copilot schema)
├── .kiro/steering/wiki-schema.md      # Kiro/Claude schema (identical content)
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

#### Entity Page
```markdown
# {Entity Name}

## Summary
Brief 2-3 sentence overview of the entity.

## Details
Detailed information organized by relevant aspects.

## Key Points
- Bullet points of the most important facts

## Sources
- [[source-summary-1]] — what this source contributes
- [[source-summary-2]] — what this source contributes

## Contradictions
(Only add this section when contradictions exist)
- **Claim A** (from [[source-1]]) vs **Claim B** (from [[source-2]]) — flagged YYYY-MM-DD
```

#### Concept Page
```markdown
# {Concept Name}

## Summary
Brief 2-3 sentence overview of the concept.

## Explanation
Detailed explanation of the concept, how it works, and why it matters.

## Examples
Concrete examples illustrating the concept.

## Related Concepts
- [[concept-1]] — how it relates
- [[concept-2]] — how it relates

## Sources
- [[source-summary-1]] — what this source contributes
```

#### Source Summary Page
```markdown
# {Source Title}

## Metadata
- **Author**: ...
- **Date**: ...
- **Format**: Markdown | PDF | DOCX | Excel | HTML | Image
- **Raw file**: [[raw/{filename}]]

## Summary
Comprehensive summary of the source's key content.

## Key Takeaways
1. First key takeaway
2. Second key takeaway
3. Third key takeaway

## Entities & Concepts Mentioned
- [[entity-1]] — how it's discussed in this source
- [[concept-1]] — how it's discussed in this source

## Quotes & Data Points
Notable quotes or data points worth preserving (with page/section references).
```

#### Comparison Page
```markdown
# {Comparison Title}

## Overview
What is being compared and why.

## Comparison

| Dimension | [[Entity/Concept A]] | [[Entity/Concept B]] |
|---|---|---|
| Aspect 1 | ... | ... |
| Aspect 2 | ... | ... |

## Analysis
Synthesis of the comparison findings.

## Sources
- [[source-summary-1]], [[source-summary-2]], ...
```

#### Synthesis Page
```markdown
# {Synthesis Title}

## Overview
What this synthesis covers and why it matters.

## Thesis
The main argument or finding from synthesizing multiple sources.

## Evidence
Supporting evidence organized by theme or source.

## Counterpoints
Any evidence that challenges the thesis.

## Conclusion
Summary of the synthesis and confidence level.

## Sources
- [[source-summary-1]], [[source-summary-2]], ...
```

#### Overview Page
```markdown
# {Topic Name}

## Introduction
High-level introduction to the topic area.

## Key Concepts
- [[concept-1]] — brief description
- [[concept-2]] — brief description

## Key Entities
- [[entity-1]] — brief description
- [[entity-2]] — brief description

## Current State
Summary of what the wiki currently knows about this topic.

## Open Questions
Questions that could be explored with additional sources.
```

#### Timeline Page
```markdown
# {Timeline Title}

## Overview
What this timeline covers.

## Timeline

### YYYY-MM — Event Title
Description of the event. ([[source-summary]])

### YYYY-MM — Event Title
Description of the event. ([[source-summary]])

## Sources
- [[source-summary-1]], [[source-summary-2]], ...
```

#### Glossary Entry Page
```markdown
# {Term}

## Definition
Clear, concise definition.

## Context
How this term is used in the wiki's domain. Related concepts: [[concept-1]], [[concept-2]].

## Sources
- [[source-summary-1]] — where this term is defined/used
```

#### Question-Answer Page
```markdown
# {Question}

## Answer
The synthesized answer based on wiki content.

## Evidence
Key points supporting the answer, with citations.

## Related Pages
- [[page-1]], [[page-2]], ...

## Sources
- [[source-summary-1]], [[source-summary-2]], ...
```

#### Debate Page
```markdown
# {Debate Title}

## Question
The contested question or claim.

## Position A: {Position Name}
Arguments and evidence supporting this position.
- [[source-1]], [[source-2]]

## Position B: {Position Name}
Arguments and evidence supporting this position.
- [[source-3]], [[source-4]]

## Current Assessment
Which position has stronger support based on available sources.
```


---

## 4. Workflows

### 4.1 Ingest Workflow

**Trigger**: User drops a file into `raw/` and asks you to process it.

**Steps**:

1. **Read the source** — Read the raw document. For PDF/DOCX/Excel/HTML, extract text content. For images, read surrounding text first, then view images separately for additional context.

2. **Discuss with user** — Present key takeaways and notable points. Ask what to emphasize or de-emphasize. (In batch mode, abbreviate or skip this step.)

3. **Create source summary** — Write a `source-summary` page in the appropriate category subdirectory. Include full frontmatter and all template sections.

4. **Identify affected pages** — Read `wiki/_master-index.md` to find existing pages related to the source's content. Follow `[[wiki links]]` to read those pages.

5. **Update existing pages** — For each affected page:
   - Add new information from the source
   - Add `[[wiki links]]` to the new source summary
   - Update frontmatter: `updated` date, `sources` array, `source-count`
   - Link every mention of known entities/concepts (liberal linking)

6. **Flag contradictions** — If the new source contradicts existing content:
   - Add a "Contradictions" section to the affected page
   - Note the disagreement with citations to both sources
   - **Do NOT change the existing content** — the human decides what's correct
   - **Notify the human** about the contradiction in your response

7. **Create new pages** — If the source introduces entities or concepts not yet in the wiki, create new pages for them with appropriate page types.

8. **Create/update categories** — If new pages don't fit existing categories, create a new subdirectory with `_index.md`. Update all affected category `_index.md` files.

9. **Update master index** — Add all new pages to `wiki/_master-index.md`. Update summaries for modified pages. Update page and category counts.

10. **Log the operation** — Append to `wiki/log.md`:
    ```
    ## [YYYY-MM-DD] ingest | {Source Title}
    {One-line summary: pages created, pages updated, contradictions flagged.}
    ```

### 4.2 Query Workflow

**Trigger**: User asks a question.

**Steps**:

1. **Parse the question** — Identify key entities, concepts, and the type of answer needed (factual lookup, comparison, synthesis, timeline, etc.).

2. **Navigate the wiki** — Read `wiki/_master-index.md` to find relevant categories and pages. Read category `_index.md` files for more detail. Follow `[[wiki links]]` to discover connected content.

3. **Read relevant pages** — Read identified pages in full. Note which sources they cite.

4. **Synthesize answer** — Compose an answer in markdown with citations to wiki pages and their underlying sources.

5. **Present the answer** — Show the answer to the user.

6. **Ask about filing** — Ask: *"Would you like to save this answer as a wiki page, save to output/, or keep it in chat only?"*

7. **Execute the user's choice**:
   - **Wiki page**: Create a new page (type: `comparison`, `synthesis`, `question-answer`, or `debate` as appropriate). Update master index, category index, and log.
   - **Output**: Save to `output/query-results/{descriptive-name}.md`. Log the query.
   - **Chat only**: Log the query (no files created).

### 4.3 Lint Workflow

**Trigger**: User requests a health check (e.g., "lint the wiki", "health check").

**Checklist** (check all 10 items):

| # | Check | Severity |
|---|---|---|
| 1 | **Contradictions** — Pages with unresolved contradictions | High |
| 2 | **Stale claims** — Content superseded by newer sources | High |
| 3 | **Orphan pages** — Pages with no inbound `[[wiki links]]` | Medium |
| 4 | **Missing pages** — Entities/concepts mentioned but lacking their own page | Medium |
| 5 | **Missing cross-references** — Related pages that don't link to each other | Medium |
| 6 | **Broken links** — `[[wiki links]]` pointing to non-existent pages | High |
| 7 | **Frontmatter issues** — Missing required fields, invalid types, date errors | Medium |
| 8 | **Index drift** — Pages missing from `_master-index.md` or category indexes | High |
| 9 | **Data gaps** — Important topics with thin coverage | Low |
| 10 | **Suggested sources** — Topics that would benefit from additional sources | Low |

**Output**: Save report to `output/lint-reports/lint-YYYY-MM-DD.md`. Present findings to user. Ask which fixes to apply. Execute approved fixes. Log the operation.

---

## 5. Conventions

### 5.1 File Naming
- Wiki pages: `kebab-case.md` (e.g., `machine-learning.md`, `attention-mechanism.md`)
- Category directories: `kebab-case/` (e.g., `natural-language-processing/`)
- Source summaries: `{descriptive-name}.md` matching the source title in kebab-case
- Index files: `_index.md` (underscore prefix keeps them sorted first)
- Master index: `_master-index.md`

### 5.2 Linking
- **Liberal linking**: Link every mention of a known entity or concept on every page using `[[wiki links]]`. This maximizes graph connectivity in Obsidian.
- **Link format**: Use `[[page-name]]` for same-name links, `[[page-name|Display Text]]` when the display text differs from the filename.
- **Aliases**: If an entity has multiple names, list them in the `aliases` frontmatter field. Obsidian will resolve links to aliases automatically.

### 5.3 Contradiction Handling
- **Never overwrite** existing content when a new source contradicts it.
- **Add a "Contradictions" section** to the affected page noting the disagreement.
- **Cite both sources** so the human can evaluate.
- **Notify the human** in your response — don't silently flag.

### 5.4 Category Creation
- Create a new category when 2+ pages would belong to a topic area not covered by existing categories.
- Always create `_index.md` immediately when creating a new category directory.
- Update `_master-index.md` to include the new category.

### 5.5 Source Handling
- Raw sources in `raw/` are **immutable** — never modify them.
- Every raw source gets exactly one `source-summary` page in the wiki.
- The source summary links back to the raw file: `[[raw/{filename}]]`.

---

## 6. Tone & Style Guide

### Writing Style
- **Clear and concise** — prefer short sentences and active voice.
- **Factual and neutral** — present information without editorializing. When opinions exist, attribute them to sources.
- **Well-structured** — use headers, bullet points, and tables to organize information. Avoid walls of text.
- **Citation-rich** — always cite sources. Use `[[wiki links]]` to source summary pages.

### Formatting
- Use `##` for major sections, `###` for subsections.
- Use bullet points for lists of facts or features.
- Use tables for comparisons and structured data.
- Use blockquotes (`>`) for notable quotes from sources.
- Keep paragraphs short (3-5 sentences max).

### Tone
- Informative, not academic. Write for a knowledgeable reader who wants quick understanding.
- Avoid jargon unless it's defined (create a `glossary-entry` page for technical terms).
- Be direct — state the key point first, then elaborate.


---

## 7. Example Pages

### Example: Entity Page
```markdown
---
title: "Transformer Architecture"
type: entity
tags: [deep-learning, architecture, nlp]
created: 2026-04-15
updated: 2026-04-15
sources: ["[[attention-is-all-you-need-summary]]"]
aliases: ["Transformer", "Transformer model"]
related: ["[[self-attention]]", "[[multi-head-attention]]"]
confidence: high
word-count: 350
source-count: 1
---

# Transformer Architecture

## Summary
The [[Transformer Architecture]] is a [[deep learning]] model architecture introduced by [[Google Brain]] in 2017. It relies entirely on [[self-attention]] mechanisms, dispensing with recurrence and convolutions.

## Details
The architecture consists of an encoder-decoder structure where both components use stacked layers of [[multi-head-attention]] and feed-forward networks. The key innovation is the [[self-attention]] mechanism, which allows the model to attend to all positions in the input sequence simultaneously.

## Key Points
- Introduced in the paper "[[attention-is-all-you-need-summary|Attention Is All You Need]]" (2017)
- Replaced [[RNN]]-based sequence models for most [[NLP]] tasks
- Foundation for [[BERT]], [[GPT]], and most modern language models
- Uses positional encoding to handle sequence order without recurrence

## Sources
- [[attention-is-all-you-need-summary]] — original paper introducing the architecture
```

### Example: Source Summary Page
```markdown
---
title: "Attention Is All You Need — Summary"
type: source-summary
tags: [paper, deep-learning, transformer, 2017]
created: 2026-04-15
updated: 2026-04-15
sources: []
aliases: []
related: ["[[transformer-architecture]]", "[[self-attention]]"]
confidence: high
word-count: 500
source-count: 0
---

# Attention Is All You Need

## Metadata
- **Author**: Vaswani et al. (Google Brain, Google Research)
- **Date**: 2017-06-12
- **Format**: PDF
- **Raw file**: [[raw/attention-is-all-you-need.pdf]]

## Summary
This paper introduces the [[Transformer Architecture]], a novel sequence transduction model based entirely on [[self-attention]] mechanisms. It achieves state-of-the-art results on machine translation benchmarks while being more parallelizable and requiring significantly less training time than [[RNN]]-based models.

## Key Takeaways
1. [[Self-attention]] can replace recurrence entirely for sequence modeling
2. [[Multi-head attention]] allows the model to attend to information from different representation subspaces
3. The [[Transformer Architecture]] achieves 28.4 BLEU on English-to-German translation, surpassing all previous models

## Entities & Concepts Mentioned
- [[Transformer Architecture]] — the main contribution of the paper
- [[Self-attention]] — the core mechanism replacing recurrence
- [[Multi-head attention]] — parallel attention heads for richer representations
- [[Google Brain]] — research lab where the work was conducted

## Quotes & Data Points
> "The Transformer allows for significantly more parallelization and can reach a new state of the art in translation quality after being trained for as little as twelve hours on eight P100 GPUs." (Section 1)
```

---

## 8. Troubleshooting

### Common Issues

**Problem**: Wiki links show as unresolved in Obsidian
- **Cause**: Page filename doesn't match the link text
- **Fix**: Ensure filenames use kebab-case and links match exactly, or add aliases to frontmatter

**Problem**: Frontmatter not rendering in Obsidian
- **Cause**: YAML syntax error (missing quotes, bad indentation)
- **Fix**: Validate YAML — ensure strings with special characters are quoted, arrays use `[]` syntax

**Problem**: Master index is out of sync with actual pages
- **Cause**: A page was created but the index wasn't updated
- **Fix**: Run a lint check — "index drift" will catch this

**Problem**: Obsidian graph view shows disconnected clusters
- **Cause**: Missing cross-references between related pages
- **Fix**: Run a lint check — "missing cross-references" will identify gaps. Ensure liberal linking on every mention.

**Problem**: Category has pages but no `_index.md`
- **Cause**: Category directory was created without its index
- **Fix**: Create `_index.md` with the standard category index format

**Problem**: Source summary doesn't link back to raw file
- **Cause**: Raw file path in the `[[raw/{filename}]]` link doesn't match actual filename
- **Fix**: Verify the raw file exists and the link path is correct

---

## 9. Changelog

### v1.0.0 — 2026-04-15
- Initial schema creation
- Defined 10 page types: entity, concept, source-summary, comparison, synthesis, overview, timeline, glossary-entry, question-answer, debate
- Defined 3 workflows: Ingest (10-step), Query (7-step), Lint (10-point checklist)
- Extended frontmatter schema: standard fields + related, confidence, word-count, source-count
- Liberal linking convention (every mention)
- Contradiction handling: flag-only, notify human
- Dynamic category creation
- Full schema sections: overview, structure, format, workflows, conventions, tone guide, examples, troubleshooting, changelog