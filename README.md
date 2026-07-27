# Academic Skills

A curated set of [Claude Agent Skills](https://agentskills.io/) for the full academic-research
workflow: **find papers → organize notes → write manuscripts/proposals → critique → sync to
your other tools**. Assembled and mirrored by [@wttntpc](https://github.com/wttntpc) for
personal use, with sources and licenses noted per skill below.

## The workflow

```
   FIND                 ORGANIZE                 WRITE                 CRITIQUE          SYNC (optional)
┌───────────┐      ┌─────────────────┐      ┌──────────────────┐   ┌──────────────┐  ┌─────────────────┐
│ litpilot  │      │ research-       │      │ scientific-      │   │              │  │ zotero-bridge   │
│ paper-    │ ───▶ │ organizer       │ ───▶ │ writing           │──▶│ peer-review  │─▶│ notebooklm-     │
│ lookup    │      │ knowledge-base  │      │ research-grants   │   │              │  │ bridge          │
└───────────┘      └─────────────────┘      └──────────────────┘   └──────────────┘  └─────────────────┘
 monitor + targeted  turn a paper into a     draft manuscripts       assess a          check/add to your
 lookup across 10+   structured note, file   AND grant proposals     manuscript or      Zotero library;
 databases           it into a long-term     with evidence-bound     preprint with      push sources into
                     personal knowledge      claims + agency-        claim-evidence      a NotebookLM
                     base                    specific guidelines     checks              notebook
```

Each stage is independent — use any single skill on its own, or chain them for a full
literature-to-manuscript pipeline. The **Sync** stage is optional and depends on what you
already use (Zotero, NotebookLM, both, or neither).

## Skills

| Skill | Stage | What it does | Source | License |
|---|---|---|---|---|
| [`litpilot`](litpilot/) | Find | Periodic literature monitoring: screens, ranks, and benchmark-compares new papers against a lab's own criteria; outputs a digest + Excel workbook + chart. Retarget it to any topic by editing `references/research_profile.md`. | Workshop teaching material (CIPH closed-loop neuromodulation research workshop, Day 1) | ⚠️ No license published — see [NOTICE](litpilot/NOTICE.md) |
| [`paper-lookup`](paper-lookup/) | Find | Targeted lookups across 10 academic APIs (PubMed, PMC, bioRxiv, medRxiv, arXiv, OpenAlex, Crossref, Semantic Scholar, CORE, Unpaywall) — DOI/PMID/arXiv-ID resolution, citation graphs, open-access PDFs. | [k-dense-ai/scientific-agent-skills](https://github.com/k-dense-ai/scientific-agent-skills) | MIT |
| [`research-organizer`](research-organizer/) | Organize | Turns one messy source (paper excerpt, notes, transcript) into a structured note with 7 fixed sections. | [ckt520728/claude-skills](https://github.com/ckt520728/claude-skills) | ⚠️ No license published — see [NOTICE](research-organizer/NOTICE.md) |
| [`knowledge-base`](knowledge-base/) | Organize | Bidirectional personal knowledge store: ingest structured notes (into an Obsidian vault or local filesystem) and query across them later with citations. Structured links, not vector RAG. | [ckt520728/claude-skills](https://github.com/ckt520728/claude-skills) | ⚠️ No license published — see [NOTICE](knowledge-base/NOTICE.md) |
| [`scientific-writing`](scientific-writing/) | Write | Draft/revise manuscripts with evidence-bound claims, IMRaD structure, reporting-guideline selection (CONSORT/PRISMA/STROBE/ARRIVE/...), authorship (ICMJE/CRediT) and consistency checks. Local-only, no network calls. | [k-dense-ai/scientific-agent-skills](https://github.com/k-dense-ai/scientific-agent-skills) | MIT |
| [`research-grants`](research-grants/) | Write | Writes competitive research proposals for NSF, NIH, DOE, DARPA, and Taiwan's NSTC — agency-specific formatting, review criteria, specific aims, budget justification. Core guidance needs no network; optional AI-generated figures need an OpenRouter key. | [k-dense-ai/scientific-agent-skills](https://github.com/k-dense-ai/scientific-agent-skills) | MIT |
| [`peer-review`](peer-review/) | Critique | Prepares evidence-bounded, constructive peer-review drafts: claim–evidence checks, methods/statistics/reproducibility/ethics/citation critique, confidentiality-first. Local-only, no network calls. | [k-dense-ai/scientific-agent-skills](https://github.com/k-dense-ai/scientific-agent-skills) | MIT |
| [`zotero-bridge`](zotero-bridge/) | Sync | Checks whether papers found by `litpilot`/`paper-lookup` already exist in your Zotero library, and (with confirmation) adds new ones — local read-only SQLite snapshot or Zotero Web API. | Original, written for this collection | MIT |
| [`notebooklm-bridge`](notebooklm-bridge/) | Sync | Pushes found papers / organized notes into a Google NotebookLM notebook for semantic Q&A and audio/video overviews. Requires a NotebookLM MCP connector configured in the host. | Original, written for this collection | MIT |

## Installation

Each skill is a self-contained folder with a `SKILL.md` (plus optional `references/`,
`assets/`, `scripts/`). Claude Code (and any host that follows the [Agent Skills](https://agentskills.io/)
standard) discovers skills by folder location — copy in what you want, no build step.
**Restart Claude Code (or start a new session) after copying** so it picks up new skills.

### Option 1 — one skill

macOS / Linux / Git Bash on Windows:
```bash
cp -r paper-lookup ~/.claude/skills/           # user-level, every project
cp -r paper-lookup /path/to/project/.claude/skills/   # project-level, one project only
```

Windows PowerShell:
```powershell
Copy-Item -Recurse paper-lookup "$env:USERPROFILE\.claude\skills\"
Copy-Item -Recurse paper-lookup "C:\path\to\project\.claude\skills\"
```

### Option 2 — the whole set

macOS / Linux / Git Bash on Windows:
```bash
git clone https://github.com/wttntpc/academic-skills- ~/academic-skills-tmp
for skill in litpilot paper-lookup research-organizer knowledge-base scientific-writing \
             research-grants peer-review zotero-bridge notebooklm-bridge; do
  cp -r ~/academic-skills-tmp/$skill ~/.claude/skills/
done
```

Windows PowerShell:
```powershell
git clone https://github.com/wttntpc/academic-skills- "$env:USERPROFILE\academic-skills-tmp"
$skills = "litpilot","paper-lookup","research-organizer","knowledge-base","scientific-writing",
          "research-grants","peer-review","zotero-bridge","notebooklm-bridge"
foreach ($s in $skills) {
  Copy-Item -Recurse "$env:USERPROFILE\academic-skills-tmp\$s" "$env:USERPROFILE\.claude\skills\"
}
```

### Portability notes (things that differ machine to machine)

- **Python command name**: `scientific-writing`, `peer-review`, and `research-grants`'
  scripts are invoked as `python3 scripts/...` in their docs. If a machine only has `python`
  on PATH (common on plain Windows installs without the `python3` alias), substitute
  `python` for `python3` when running the commands shown in each skill's `SKILL.md`. This is
  a command-name difference only — the scripts themselves are pure standard library and run
  identically either way.
- **`knowledge-base`**: needs the `mcp-obsidian` MCP server configured to write into a real
  Obsidian vault; without it, falls back to a local filesystem folder automatically.
- **`zotero-bridge`**'s local-SQLite backend only works on the machine that has Zotero
  desktop installed and running; the Web-API backend works from any machine with an API key.
- **`notebooklm-bridge`** needs a NotebookLM MCP connector configured in the host — it does
  not work out of the box on a fresh Claude Code install without that connector added first.
- **`paper-lookup`**: works with no API key at a lower rate limit; add `NCBI_API_KEY`,
  `CORE_API_KEY`, `S2_API_KEY`, or `OPENALEX_API_KEY` as environment variables for higher
  limits (see `paper-lookup/references/`).
- **`litpilot`**: edit `references/research_profile.md` to your own topic, keywords,
  inclusion criteria, and (optionally) benchmark values before your first run — the version
  in this repo is the generic template, not tuned to any particular topic.

## Why these nine, together

Most literature tools stop at "find papers" or "summarize this PDF." This set covers the
whole loop a researcher actually needs:

1. **litpilot / paper-lookup** — you can't organize or write about what you haven't found.
2. **research-organizer / knowledge-base** — a found paper is only useful if it survives
   past the browser tab; this turns it into a durable, linked, citable note.
3. **scientific-writing / research-grants** — turns verified notes into manuscript or
   proposal prose without letting the model invent citations, numbers, methods, or agency
   requirements along the way.
4. **peer-review** — the same evidence-discipline applied in the other direction: assessing
   someone else's manuscript instead of writing your own.
5. **zotero-bridge / notebooklm-bridge** — optional glue so what this pipeline finds and
   writes doesn't just live in a chat transcript; it lands in the reference manager and
   notebook tool you actually already use.

## Notes on the unlicensed skills

`litpilot`, `research-organizer`, and `knowledge-base` are mirrored here without a published
open-source license from their original authors (see each skill's `NOTICE.md`). They are
included for personal/educational reference. If you are not the uploader and want to reuse,
modify, or redistribute them beyond personal use, please go to the linked upstream repository
and ask the original author directly rather than relying on this mirror.
