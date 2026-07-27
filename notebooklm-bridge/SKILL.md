---
name: notebooklm-bridge
description: Sync papers, digests, and research notes from this skill set (litpilot digests, research-organizer notes, knowledge-base entries) into a Google NotebookLM notebook for semantic Q&A and audio/video overviews. Trigger on "add this to my NotebookLM", "sync to notebooklm", "加進 NotebookLM", "同步到 NotebookLM", "make a notebook for this topic". Requires the host to have a NotebookLM MCP connector configured — this skill is guidance for using it, not the connector itself.
license: MIT
metadata:
  version: "1.0"
  skill-author: wttntpc
---

# NotebookLM Bridge

Documents how the literature-workflow skills in this collection (`litpilot`, `paper-lookup`,
`research-organizer`, `knowledge-base`) hand off to a NotebookLM notebook for semantic
search, cross-source Q&A, and audio/video overviews.

## Prerequisite — this is a connector, not a script

Unlike `zotero-bridge`, there is no plain REST API this skill calls directly. It relies on a
**NotebookLM MCP server being configured in the host** (tool names look like
`mcp__notebooklm__*` or `mcp__notebooklm-mcp__*`, providing `notebook_create`, `source_add`,
`notebook_query`, and related tools). If those tools aren't available, tell the user plainly
that NotebookLM sync needs that MCP server set up first (their host's documentation covers
adding it — an `nlm login` style auth step is a common pattern for these connectors), and
either stop or offer to proceed without this step.

**Do not assume this connector exists — check for it before promising sync will work.**

## Workflow

### 1. Decide notebook scope

Ask, or infer from context, whether this should go into:
- an **existing** notebook for the same research topic (ask the user to name it, or list
  notebooks and let them pick), or
- a **new** notebook (e.g. one per `litpilot` research profile / topic).

Don't create a new notebook per run by default — that fragments a running literature review
across dozens of notebooks. Reuse the topic's notebook if one already exists.

### 2. Add sources

- From a `litpilot` digest: add the underlying papers (their DOI/URL, or the PDF if the user
  has it locally), not the digest text itself — NotebookLM should hold primary sources, and
  the digest itself belongs in the chat/Excel/chart outputs `litpilot` already produces.
- From `research-organizer` / `knowledge-base` notes: add the *original source material* the
  note was built from (the paper, not the note) when available, so NotebookLM's citations
  point at real sources rather than a secondary summary. If the user only kept the note (no
  original file), adding the note itself is a reasonable fallback — say which one you're doing.
- Use the connector's source-add tool (e.g. `source_add`) per item. Batch, but don't silently
  skip failures — report which sources succeeded and which didn't.

### 3. Optional — query back

Once sources are in, `notebook_query` (or equivalent) can answer cross-paper questions
directly from NotebookLM's index. This is a second, independent retrieval path alongside
`knowledge-base`'s Obsidian/filesystem search — useful when the question benefits from
NotebookLM's semantic search across full-text sources rather than the user's own structured
notes and links.

### 4. Report back

- Which notebook the sources landed in (name or ID).
- How many sources added vs. how many failed, with a one-line reason for any failure.
- **Don't** dump the full NotebookLM response into chat — link/name is enough; the user will
  open the notebook themselves.

## Where this fits in the pipeline

```
litpilot / paper-lookup  →  research-organizer / knowledge-base  →  notebooklm-bridge (optional)
        (find)                        (organize)                    (semantic search / overview)
```

Treat NotebookLM as an **additional** retrieval surface, not a replacement for
`knowledge-base`'s structured, linked notes — the two answer different kinds of questions
(semantic full-text search vs. "what did I personally conclude about X, and when").

## Safety notes

- Confirm before creating a *new* notebook — don't spawn one silently on every run.
- If a source is confidential/unpublished (e.g. a manuscript under peer review), do not add
  it to NotebookLM without the same authorization check `peer-review` and `scientific-writing`
  already require for external services — NotebookLM sources are sent to Google's service.
