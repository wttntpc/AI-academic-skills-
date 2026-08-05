# Workflow: grounding a grant proposal in your existing library + new literature

No single skill in this collection does this end-to-end — it is a manual composition of
five existing skills, written down here because the composition itself is easy to forget a
step of. Use this when you're about to draft (or substantially revise) a **grant proposal**
and want its theoretical foundation to rest on more than whatever `litpilot` happened to
surface in its default monitoring window.

## The gap this fills

- `litpilot` defaults to a **rolling 30-day window** (7 days for scheduled weekly runs, see
  `litpilot/SKILL.md` Step 2 and `references/research_profile.md`). That is correct for "what's
  new this month" but wrong for "what does the field already know" — a grant's Significance /
  Background section usually needs literature spanning years, not weeks.
- `zotero-bridge`'s documented workflow (`zotero-bridge/SKILL.md`, "Workflow: after a litpilot /
  paper-lookup run") is **new-papers-in → check-against-library**. It was not written to pull your
  *existing* curated library out as a corpus to feed into appraisal or writing.
- `notebooklm-bridge` does support querying back (`notebooklm-bridge/SKILL.md`, step 3), but
  nothing in this collection automatically routes that synthesis into `research-grants`.

None of that is a bug — each skill does one job well. This doc is the missing glue.

## Prerequisites

| Skill | Needed for |
|---|---|
| `zotero-bridge` | Reading what you've already collected on the topic |
| `litpilot` | Filling the "what's new" gap, widened past its 30-day default |
| `paper-lookup` | Targeted lookups for specific known papers/authors |
| `paper-review` (+ `paper-digest` for lighter passes) | Appraising *every* candidate before it counts as evidence — old or new |
| `notebooklm-bridge` | Cross-paper synthesis once appraised sources are in one notebook |
| `research-grants` (+ `scientific-writing` for the manuscript-shaped sections) | Turning appraised, synthesized evidence into agency-formatted prose |

A NotebookLM MCP connector must be configured for the `notebooklm-bridge` steps (see that
skill's SKILL.md); a Zotero Web API key or local Zotero desktop install is needed for the
`zotero-bridge` steps.

## Steps

### 1. Scope the topic and override litpilot's default window

State the topic and an explicit date range up front — don't rely on the 30-day default for
this pass. Either say it in chat ("search the last 5 years, not just the last 30 days") or
edit `litpilot/references/research_profile.md` for a standing change if this topic is
long-running.

### 2. Pull what you already trust

Ask for your existing Zotero collection on the topic (`zotero-bridge`'s read path — local
SQLite snapshot or Web API lookup). This is not the skill's advertised trigger, but its
read mechanism works for it: you're listing/searching your library rather than checking one
paper against it. Treat this as your starting evidence pool, not the final one — it reflects
what you found and kept in the past, which can lag the field or reflect an earlier framing of
the question.

### 3. Fill the gaps with new literature

Run `litpilot` with the widened window from Step 1, and use `paper-lookup` for any specific
paper or author you already know you need but that a keyword search might miss. De-duplicate
against the Step 2 list by DOI before moving on — you don't want to re-appraise something
already in your library.

### 4. Appraise everything before it counts as evidence

Every candidate from Steps 2 and 3 — old or new — goes through `paper-review` (deep tier for
anything that will anchor a key claim; `paper-digest`, or `paper-review`'s quick tier, for
supporting citations). This is the step most tempting to skip for papers "already in my
library because I trust them" — don't skip it. A paper you added to Zotero two years ago
under a different research question hasn't been checked against *this* proposal's claims.

### 5. Synthesize across sources

Push the appraised set into a topic NotebookLM notebook (`notebooklm-bridge`) and use
`notebook_query` to ask cross-paper questions the proposal needs answered — e.g., "what
effect sizes has this literature reported for X, and where do they disagree?" This catches
contradictions and gaps a paper-by-paper read can miss.

### 6. Write with evidence-bound claims

Hand the appraised papers + NotebookLM synthesis to `research-grants` (agency-specific
structure, e.g. NSTC's CM03 architecture diagram, NIH's Specific Aims) or `scientific-writing`
for a manuscript-shaped section. Both skills already refuse to invent citations, numbers, or
methods — they need the verified evidence handed to them, not asked to go find it themselves
mid-draft.

## Worked example (chat-level, not code)

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

## Safety notes

- **NotebookLM is a third-party service.** Anything pushed to it leaves your machine and goes
  to Google. Don't push an unpublished manuscript or confidential preliminary data without the
  same authorization check `peer-review`/`scientific-writing` already require elsewhere in this
  collection.
- **Zotero writes still need explicit confirmation** — Step 2 here is read-only by design; if a
  new paper found in Step 3 should be *added* to Zotero, that's a separate, confirmed action per
  `zotero-bridge`'s own safety notes, not an automatic side effect of this workflow.
- This is a **deep-pass workflow**, not a daily habit — the token/time cost of appraising a full
  evidence base is real. Reach for it before a substantial proposal draft, not for routine
  literature monitoring (that's what `litpilot`'s default 30-day cadence is for).
