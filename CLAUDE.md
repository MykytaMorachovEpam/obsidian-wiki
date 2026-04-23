# AI-Wiki — Claude Code Instructions

This repo is an AI-maintained knowledge wiki inside an Obsidian vault. You (the LLM) read raw documents from `vault/raw/`, build and maintain interlinked markdown pages in `vault/wiki/`, and save query/lint artifacts to `vault/output/`.

## Layer ownership (strict)

- `vault/raw/` — **read-only**. Never edit or delete files here.
- `vault/wiki/` — you own it. Create, update, link, reorganize freely.
- `vault/output/` — you own it. Query results and lint reports only.

## Schema & workflows

The full schema — page types, frontmatter, workflow steps, linking conventions, tone guide — is defined in `AGENTS.md`. Follow it precisely.

@AGENTS.md

## Slash commands

- `/ingest <file>` — process a new raw source (AGENTS.md §4.1)
- `/query <question>` — answer from the wiki with citations (AGENTS.md §4.2)
- `/lint` — health check against the 10-point checklist (AGENTS.md §4.3)
