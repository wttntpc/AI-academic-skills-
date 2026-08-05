---
name: grant-evidence-base
description: >
  Orchestrates litpilot, zotero-bridge, paper-lookup, paper-review, and notebooklm-bridge to
  build a multi-year evidence base for a grant proposal's Significance/Background section —
  combining the user's existing Zotero library with new literature (past litpilot's default
  30-day monitoring window), appraising all of it through paper-review, and synthesizing it
  via NotebookLM's query-back — before handing off to research-grants or scientific-writing.
  Trigger on "before I write this grant proposal", "build my evidence base", "ground this
  proposal in the literature", "把 Zotero 跟 NotebookLM 一起評讀", "既有文獻庫", "計畫書文獻
  基礎", "文獻證據庫", or when the user is about to draft a Significance/Background section
  and wants more than litpilot's default monitoring window. NOT for routine weekly literature
  monitoring (litpilot alone already does that) and NOT for a single known paper (paper-lookup
  or paper-review alone is enough).
license: MIT
metadata:
  version: "1.0"
  skill-author: wttntpc
---

# Grant Evidence Base

This skill does not do any new work itself — it orchestrates five other skills in this
collection in a specific order, for a specific gap none of them close alone: none of
`litpilot`, `zotero-bridge`, `paper-lookup`, `paper-review`, or `notebooklm-bridge` combines
"what I already collected" with "what's new" and appraises both before a grant proposal is
drafted. Use it as the entry point when that composition is what's needed, not as a
replacement for calling any of those skills directly for their own narrower job.

## The gap this closes

- `litpilot` defaults to a **rolling 30-day window** (7 days for scheduled weekly runs — see
  `litpilot/SKILL.md` Step 2 and `references/research_profile.md`). Correct for "what's new
  this month," wrong for "what does the field already know" — a grant's Significance /
  Background section usually needs literature spanning years, not weeks.
- `zotero-bridge`'s documented workflow (see `zotero-bridge/SKILL.md`, "Workflow: after a
  litpilot / paper-lookup run") is **new-papers-in → check-against-library**. It wasn't
  written to pull the *existing* curated library out as a corpus for appraisal or writing.
- `notebooklm-bridge` supports querying back (`notebooklm-bridge/SKILL.md`, step 3), but
  nothing routes that synthesis into `research-grants` automatically.

Each of those skills does its own job well; this skill is the sequencing on top.

## Orchestrates (does not replace)

| Skill | Role in this sequence |
|---|---|
| `zotero-bridge` | Reads the existing library on the topic (its lookup mechanism, generalized past single-DOI checks) |
| `litpilot` | Fills the "what's new" gap, run with a window explicitly widened past its 30-day default |
| `paper-lookup` | Targeted lookups for specific known papers/authors a keyword search might miss |
| `paper-review` (or `paper-digest` for lighter passes) | Appraises **every** candidate — old or new — before it counts as evidence |
| `notebooklm-bridge` | Cross-paper synthesis once the appraised sources are in one notebook |
| `research-grants` (or `scientific-writing` for manuscript-shaped sections) | Turns the appraised, synthesized evidence into agency-formatted prose |

A NotebookLM MCP connector must be configured for the `notebooklm-bridge` steps; a Zotero Web
API key or local Zotero desktop install is needed for the `zotero-bridge` steps. If either
prerequisite is missing, say so and either stop or offer to proceed with a reduced version
(e.g. skip NotebookLM synthesis and hand `paper-review`'s output straight to `research-grants`).

## Steps

### 1. Scope the topic and override litpilot's default window

State the topic and an explicit date range up front — don't rely on the 30-day default for
this pass. Either take it from the user's message ("search the last 5 years, not just the
last 30 days") or, for a long-running topic, offer to edit
`litpilot/references/research_profile.md` as a standing change.

### 2. Pull what the user already trusts

Call `zotero-bridge`'s read path (local SQLite snapshot or Web API lookup) to list/search the
existing Zotero collection on the topic. This is not that skill's advertised trigger, but its
read mechanism generalizes to it. Treat the result as a starting evidence pool, not the final
one — it reflects what was found and kept in the past, which can lag the field or reflect an
earlier framing of the question.

### 3. Fill the gaps with new literature

Run `litpilot` with the widened window from Step 1, and use `paper-lookup` for any specific
paper or author already known but that a keyword search might miss. De-duplicate against the
Step 2 list by DOI before moving on — don't re-appraise something already in the library.

### 4. Appraise everything before it counts as evidence

Every candidate from Steps 2 and 3 — old or new — goes through `paper-review` (deep tier for
anything anchoring a key claim; `paper-digest`, or `paper-review`'s quick tier, for supporting
citations). This is the step most tempting to skip for papers "already in the library because
they're trusted" — don't skip it. A paper added to Zotero years ago under a different research
question hasn't been checked against *this* proposal's claims.

### 5. Synthesize across sources

Push the appraised set into a topic NotebookLM notebook (`notebooklm-bridge`) and use
`notebook_query` to ask cross-paper questions the proposal needs answered — e.g., "what effect
sizes has this literature reported for X, and where do they disagree?" This catches
contradictions and gaps a paper-by-paper read can miss.

### 6. Write with evidence-bound claims

Hand the appraised papers + NotebookLM synthesis to `research-grants` (agency-specific
structure, e.g. NSTC's CM03 architecture diagram, NIH's Specific Aims) or `scientific-writing`
for a manuscript-shaped section. Both already refuse to invent citations, numbers, or methods
— they need the verified evidence handed to them, not asked to go find it mid-draft.

## Worked example (chat-level)

```
1. "topic: aerobic exercise x resting-state EEG x cognition in older adults,
    search the last 6 years, not the default 30-day window"
2. "list what's already in my Zotero library on this topic"           → zotero-bridge (read)
3. "run litpilot on that topic + window; also look up [specific known paper]" → litpilot + paper-lookup
4. "de-dupe steps 2+3 by DOI, then paper-review every one — deep tier
    for anything I'd cite for a headline effect size"                  → paper-review
5. "push the appraised set into my [topic] NotebookLM notebook, then
    ask it where reported effect sizes for X disagree across studies"  → notebooklm-bridge
6. "draft the NSTC CM03 Significance section from this evidence set"   → research-grants
```

## Where this fits in the pipeline

```
zotero-bridge (existing)  ┐
litpilot / paper-lookup   ┼─▶  paper-review (appraise all)  ─▶  notebooklm-bridge (synthesize)  ─▶  research-grants / scientific-writing
     (new)                ┘
```

This is a deliberate detour off the main Find→Appraise→Organize→Write→Critique→Sync pipeline
described in the top-level README — it merges an *existing* Sync-stage asset (the Zotero
library) back into Find/Appraise, specifically to feed Write, rather than following the linear
order.

## Safety notes

- **NotebookLM is a third-party service.** Anything pushed to it leaves the machine and goes
  to Google. Don't push an unpublished manuscript or confidential preliminary data without the
  same authorization check `peer-review`/`scientific-writing` already require elsewhere in this
  collection.
- **Zotero writes still need explicit confirmation** — Step 2 here is read-only by design; if a
  new paper found in Step 3 should be *added* to Zotero, that's a separate, confirmed action per
  `zotero-bridge`'s own safety notes, not an automatic side effect of this skill.
- This is a **deep-pass workflow**, not a daily habit — the token/time cost of appraising a full
  evidence base is real. Reach for it before a substantial proposal draft, not for routine
  literature monitoring (that's what `litpilot`'s default 30-day cadence is for).
