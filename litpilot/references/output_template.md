# LitPilot Digest — output template

Follow this layout every time. The goal: a PI understands the week's literature
from the tables alone, and can act without opening a single PDF.

**No-fabrication rule (hard requirement):** never invent a PMID, DOI, Impact
Factor, or journal ranking. If a field cannot be found, write "無" (or "待確認"
for journal ranking specifically) and move on — a missing field must never stop
or skip the rest of a paper's summary.

**Journal quality metric — use OpenAlex, always labelled as such:** official
Clarivate JCR Impact Factor is paywalled and not fetchable by this skill. Instead,
query `https://api.openalex.org/sources?search=<journal name>` (free, no key) and
report `summary_stats.2yr_mean_citedness` and `h_index`. Always label this
explicitly as **"OpenAlex 指標(非官方 JCR)"** next to the number — never present
it as if it were the real Impact Factor, and never silently substitute one for the
other. If OpenAlex has no match, write "無".

**Article-type filter:** prioritize Original Article, Systematic Review, and
Meta-analysis. Skip conference abstracts, editorials, and book reviews unless
directly relevant to a must-have criterion.

---

## Bottom line (1 sentence)
> One plain sentence: what is the single most important thing that happened in
> the literature this period, and does it move the field relative to benchmark?

## 1. Ranked summary
A table, highest relevance first.

| # | Paper (short) | Type | N | Headline result | Relevance | Why it matters (1 line) |
|---|---|---|---|---|---|---|
| 1 | First-author Year | RCT/OL/case/preprint | — | key number | 🟢 92 | one line |

- Relevance = 0–100 with the colour dot (🟢 High ≥85 / 🟡 Medium 60–84 / ⚪ Low <60).
- Keep "Headline result" to a single number or phrase.

## 2. Benchmark comparison
This is the section that must never be skipped. Put each quantitative finding
next to the field standard.

| Finding (paper) | New value | Field benchmark | Δ vs benchmark | Read |
|---|---|---|---|---|
| e.g. Response rate | 58% | ~48% (Reddy 2024) | +10 pts | ▲ above — but open-label, treat cautiously |

Use ▲ above / ≈ at / ▼ below. Add one plain-language "Read" note so the number is interpreted, not just shown.

## 3. Paper cards (High + Medium only)
One compact card each. Skip Low-relevance papers or list them in one line at the end.

### [🟢/🟡 score] First-author et al., Year — Journal
- **Link:** direct clickable URL (PubMed page or DOI link — must open in a browser)
- **Journal quality (OpenAlex 指標,非官方 JCR):** 2yr_mean_citedness + h-index, e.g. "9.38 / h-index 105 (OpenAlex,非官方 JCR)" — write "無" if not found
- **Journal ranking:** SCI / SSCI / Scopus quartile — write "待確認" if not found
- **Article type:** Original Article / Systematic Review / Meta-analysis / other
- **Cross-topic:** which of the 5 profile topics this paper hits, if 2+ (else omit)
- **TL;DR:** one sentence a non-specialist understands.
- **Design / N:** …
- **Key numbers:** … (pull the actual statistics)
- **vs benchmark / prior work:** above/at/below, by how much, and whether the comparison is fair.
- **Relevance to us:** why this lab specifically should care.
- **Follow-up:** the concrete next action (read full text / try method / email author / add to journal club).

## 4. Follow-ups this week
A short action list, not prose:
- [ ] Read full text of #1 (strongest sham-controlled result)
- [ ] Add method X from #3 to the pipeline backlog
- [ ] Watch author group Y — second closed-loop paper this quarter

## Sources
Numbered list linking every cited paper.
