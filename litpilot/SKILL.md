---
name: litpilot
description: LitPilot is a research-assistant (RA) agent that monitors and reviews new scientific literature for a lab and returns a comparative, benchmark-aware digest plus an Excel comparison workbook and a visualization. Use this skill whenever the user wants to check for new papers, review recent literature, do a lit review, find out what is new in a topic or field, keep up to date with an area, screen a set of papers, decide which papers matter, or compare findings to the standard or benchmark. Also trigger when the user gives a topic plus inclusion criteria and asks what is worth reading, or asks for a recurring weekly literature update. Built for the closed-loop neuromodulation in MDD lab, but works for any research topic by editing the profile. Trigger on the intent even if the user never says the word LitPilot.
---

# LitPilot — Literature Review / RA Agent

LitPilot acts like a diligent research assistant who reads the new literature
every week so the lab does not have to. The job is not to dump abstracts. It is
to **screen, rank, and interpret** new papers against the lab's own criteria and
against the **established benchmarks of the field**, then hand back something a
busy PI can read in three minutes and act on — as a written digest, a
comparison spreadsheet, and a visual.

The three qualities every LitPilot output must have:

1. **Comparative** — every finding is placed next to prior work and the
   field-standard benchmark, never reported in isolation.
2. **Easy to interpret** — a ranked table first, colour-coded relevance, plain
   language, key numbers pulled out. No wall of text.
3. **Actionable / followable** — each paper ends with a concrete "so what" and a
   suggested next step, so the reader knows what to do, not just what happened.

---

## Step 1 — Load the research profile

Read the profile from `references/research_profile.md`. It contains the topic,
keywords, must-have and nice-to-have criteria, exclusions, and the reference
benchmarks. If the user gives new criteria in their message (e.g. "only human
studies, last 30 days"), those override the profile for this run. For a
different topic, ask for the topic and criteria or point the user to edit the
profile.

## Step 2 — Search the literature (cast a wide net)

Query several databases, breadth first then filter. Direct tools:

- **PubMed** (`search_articles`) — peer-reviewed biomedical literature. Primary source.
- **bioRxiv / medRxiv** (`search_preprints`) — pre-peer-review, newest signals.
- **Consensus** (`search`) — cross-database semantic search + citation counts.
- **ClinicalTrials.gov** (`search_trials`) — optional, for the trial pipeline.

**Broader coverage / targeted lookups:** if the `paper-lookup` skill is installed,
use it for arXiv, PMC full text, CORE, Unpaywall open-access links, Europe PMC,
OpenAlex, Semantic Scholar, and CrossRef — it already knows the right endpoint,
identifier format, and rate limit per database, so don't re-derive that logic
here. If `paper-lookup` isn't installed, query Europe PMC
(`https://www.ebi.ac.uk/europepmc/webservices/rest/search`), OpenAlex
(`https://api.openalex.org/works`), Semantic Scholar
(`https://api.semanticscholar.org/graph/v1/paper/search`), or CrossRef
(`https://api.crossref.org/works`) directly via web fetch — all free, no key
required.

Guidance: default window is the **last 30 days** (last 7 days for scheduled
weekly runs). Cast a wide net — aim to screen **60–100+ candidates across
sources**, then **de-duplicate by DOI / title** before filtering. Keep only
papers clearing the must-have criteria. Casting wider improves recall; the digest
still shows only High/Medium papers, so it stays short. If a search returns
nothing, loosen the query and retry rather than reporting "no results".

## Step 3 — Score and extract

For each paper that clears the must-have criteria, extract design (RCT /
open-label / case / preprint / review), N, population, the headline quantitative
result, what is new versus prior work, and a **relevance score 0–100** with a
one-line reason.

- 85–100 (High): multiple must-haves, strong design, directly usable.
- 60–84 (Medium): relevant but weaker design, tangential method, or preprint.
- 0–59 (Low): keyword match only; mention briefly or drop.

## Step 4 — Compare against the standard benchmark

For every quantitative finding, place it next to the field-standard benchmark.
If the profile defines fixed benchmark values, use those directly. If the
profile instead calls for **dynamic** benchmarking (no single fixed number
fits the field), find the most relevant recent meta-analysis / systematic
review / representative study for that specific sub-topic and use its
reported value as the comparison point — always naming that source inline.
State whether the new result is **above, at, or below** benchmark and by how
much. Never fabricate a statistic; if no benchmark can be found or a value is
missing, mark it "not reported" / "no established benchmark found" and flag
it as a follow-up.

## Step 5 — Produce THREE deliverables

Every run produces all three, so the reader can skim, compare, and share:

1. **The written digest** (in chat) — follow `references/output_template.md`:
   bottom line, ranked table, benchmark comparison table, paper cards, follow-ups.
2. **An Excel comparison workbook** (`.xlsx`) — follow
   `references/comparison_outputs.md`. This is the sortable, shareable artifact
   with a ranked sheet and a benchmark-comparison sheet, colour-coded.
3. **A visualization** — a benchmark comparison chart (new findings vs field
   standard) saved as an image, or a small self-contained HTML dashboard. Also
   per `references/comparison_outputs.md`.

Build the workbook/visual with the spreadsheet and charting tools available in
the environment (e.g. the xlsx skill / Python: openpyxl, pandas, matplotlib).
Always present the files to the user and end the chat digest with a **Sources**
list linking each cited paper.

## Step 6 — Deliver and (optionally) schedule

LitPilot is most useful running on a cadence. The lab's default cadence is
**every Monday at 18:00 (6:00 PM)**: run a 7-day window, produce the three
deliverables, and **share the update** — present the files and, if a channel is
configured, send them (e.g. save to Google Drive / Notion, or email). When first
set up, offer this: "Want me to run LitPilot every Monday at 6 PM and share the
weekly update automatically?" To schedule, use a weekly Monday 18:00 trigger.
Keep scheduled digests short (7-day window) so each update stays fresh.

**Optional sync after delivery:** if `zotero-bridge` is installed, offer to check
the run's papers against the user's Zotero library and add the new ones (only
with explicit confirmation — see that skill's safety notes). If
`notebooklm-bridge` is installed and a NotebookLM connector is configured, offer
to push the High/Medium papers into the topic's NotebookLM notebook. Both are
optional, one-line offers — don't run them unasked.

## Reference files

- `references/research_profile.md` — topic, criteria, and benchmark values. Edit to retarget LitPilot.
- `references/output_template.md` — the exact written-digest layout.
- `references/comparison_outputs.md` — the Excel workbook and visualization specs.
- `references/example_digest.md` — a filled-in example on real papers.

## Building a proposal's evidence base, not just a weekly digest

This skill's default 30-day (or 7-day) window is tuned for "what's new," not for compiling
the multi-year literature base a grant proposal's Significance section needs. If that's the
goal, override the window explicitly (see Step 2) and see the
[`grant-evidence-base`](../grant-evidence-base/) skill, which orchestrates a widened LitPilot
run together with your existing Zotero library, `paper-review` appraisal, and
`notebooklm-bridge` synthesis before handing anything to `research-grants`.
