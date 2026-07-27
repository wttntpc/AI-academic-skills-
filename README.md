# Academic Skills

A curated set of [Claude Agent Skills](https://agentskills.io/) for the full academic-research
workflow: **find papers → organize notes → write manuscripts → critique/review**. Assembled
and mirrored by [@wttntpc](https://github.com/wttntpc) for personal use, with sources and
licenses noted per skill below.

## The workflow

```
   FIND                    ORGANIZE                  WRITE                  CRITIQUE
┌───────────┐          ┌─────────────────┐      ┌──────────────────┐   ┌──────────────┐
│ litpilot  │          │ research-       │      │ scientific-      │   │ peer-review  │
│ paper-    │ ───────▶ │ organizer       │ ───▶ │ writing           │──▶│              │
│ lookup    │          │ knowledge-base  │      │                   │   │              │
└───────────┘          └─────────────────┘      └──────────────────┘   └──────────────┘
 weekly/ad-hoc          turn a paper into        draft manuscripts       assess a
 literature             a structured note,       with evidence-bound     manuscript/
 monitoring +           then file it into a      claims, IMRaD           preprint with
 targeted DOI/          long-term personal        structure, reporting   claim-evidence
 citation lookup        knowledge base            guidelines             checks
```

Each stage is independent — use any single skill on its own, or chain them for a full
literature-to-manuscript pipeline.

## Skills

| Skill | Stage | What it does | Source | License |
|---|---|---|---|---|
| [`litpilot`](litpilot/) | Find | Periodic literature monitoring: screens, ranks, and benchmark-compares new papers against a lab's own criteria; outputs a digest + Excel workbook + chart. Retarget it to any topic by editing `references/research_profile.md`. | Workshop teaching material (CIPH closed-loop neuromodulation research workshop, Day 1) | ⚠️ No license published — see [NOTICE](litpilot/NOTICE.md) |
| [`paper-lookup`](paper-lookup/) | Find | Targeted lookups across 10 academic APIs (PubMed, PMC, bioRxiv, medRxiv, arXiv, OpenAlex, Crossref, Semantic Scholar, CORE, Unpaywall) — DOI/PMID/arXiv-ID resolution, citation graphs, open-access PDFs. | [k-dense-ai/scientific-agent-skills](https://github.com/k-dense-ai/scientific-agent-skills) | MIT |
| [`research-organizer`](research-organizer/) | Organize | Turns one messy source (paper excerpt, notes, transcript) into a structured note with 7 fixed sections (summary, key ideas, technical terms, actionable insights, quotable data, open questions, related work). | [ckt520728/claude-skills](https://github.com/ckt520728/claude-skills) | ⚠️ No license published — see [NOTICE](research-organizer/NOTICE.md) |
| [`knowledge-base`](knowledge-base/) | Organize | Bidirectional personal knowledge store: ingest structured notes (into an Obsidian vault or local filesystem) and query across them later with citations. Structured links, not vector RAG. | [ckt520728/claude-skills](https://github.com/ckt520728/claude-skills) | ⚠️ No license published — see [NOTICE](knowledge-base/NOTICE.md) |
| [`scientific-writing`](scientific-writing/) | Write | Draft/revise manuscripts with evidence-bound claims (every fact needs a human-verified evidence ID), IMRaD structure, reporting-guideline selection (CONSORT/PRISMA/STROBE/ARRIVE/...), authorship (ICMJE/CRediT) and consistency checks. Local-only, no network calls. | [k-dense-ai/scientific-agent-skills](https://github.com/k-dense-ai/scientific-agent-skills) | MIT |
| [`peer-review`](peer-review/) | Critique | Prepares evidence-bounded, constructive peer-review drafts: claim–evidence checks, methods/statistics/reproducibility/ethics/citation critique, confidentiality-first (never sends unpublished manuscripts to an external service). Local-only, no network calls. | [k-dense-ai/scientific-agent-skills](https://github.com/k-dense-ai/scientific-agent-skills) | MIT |

## Installation

Each skill is a self-contained folder with a `SKILL.md` (plus optional `references/`,
`assets/`, `scripts/`). Claude Code (and any host that follows the [Agent Skills](https://agentskills.io/)
standard) discovers skills by folder location — copy in what you want, no build step.

### Option 1 — one skill

```bash
# project-level (only this project can use it)
cp -r paper-lookup /path/to/your-project/.claude/skills/

# user-level (every project can use it)
cp -r paper-lookup ~/.claude/skills/
```

Restart Claude Code (or start a new session) so it picks up the new skill.

### Option 2 — the whole set

```bash
git clone https://github.com/wttntpc/academic-skills- ~/academic-skills-tmp
for skill in litpilot paper-lookup research-organizer knowledge-base scientific-writing peer-review; do
  cp -r ~/academic-skills-tmp/$skill ~/.claude/skills/
done
```

### After installing

- **`litpilot`**: edit `references/research_profile.md` to your own topic, keywords, inclusion
  criteria, and (optionally) benchmark values before your first run.
- **`knowledge-base`**: if you use Obsidian, configure the `mcp-obsidian` MCP server first so
  notes land in your real vault; otherwise it falls back to a local `knowledge-base/` folder.
- **`scientific-writing`** / **`peer-review`**: pure Python 3.11+ standard library scripts,
  no API keys, no network calls — works offline out of the box.
- **`paper-lookup`**: works with no API key at a lower rate limit; add `NCBI_API_KEY`,
  `CORE_API_KEY`, `S2_API_KEY`, or `OPENALEX_API_KEY` as environment variables for higher limits
  (see `paper-lookup/references/`).

## Why these six, together

Most literature tools stop at "find papers" or "summarize this PDF." This set covers the
whole loop a researcher actually needs:

1. **litpilot / paper-lookup** — you can't organize or write about what you haven't found.
2. **research-organizer / knowledge-base** — a found paper is only useful if it survives
   past the browser tab; this turns it into a durable, linked, citable note.
3. **scientific-writing** — turns verified notes into manuscript prose without letting the
   model invent citations, numbers, or methods along the way.
4. **peer-review** — the same evidence-discipline applied in the other direction: assessing
   someone else's manuscript instead of writing your own.

## Notes on the unlicensed skills

`litpilot`, `research-organizer`, and `knowledge-base` are mirrored here without a published
open-source license from their original authors (see each skill's `NOTICE.md`). They are
included for personal/educational reference. If you are not the uploader and want to reuse,
modify, or redistribute them beyond personal use, please go to the linked upstream repository
and ask the original author directly rather than relying on this mirror.
