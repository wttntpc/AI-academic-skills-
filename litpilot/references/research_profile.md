# Research Profile — edit this to retarget LitPilot

This block defines what LitPilot screens for. Everything below is meant to be
edited by the researcher. Keep it short and specific — vague criteria produce
vague digests.

## Topic
有氧運動介入對心肺適能(VO2peak/VO2max)、認知表現(執行功能)、靜息態腦波(EEG)、
心率變異度(HRV)與情緒的影響 — 對象為一般成人/大學生族群，對應目前進行中的
12 週有氧運動介入研究(身體組成、體適能、CIPH APP 認知測驗、6 分鐘睜閉眼靜息態
EEG、情緒問卷)。

## Keywords (search seeds)

分成 5 個主題分別下去搜(不要只用合併的 AND 字串,以免漏掉單一主題但仍相關的重要
文獻)。單篇論文若同時命中 2 個以上主題,在 digest 裡明確標註「跨主題」並列為最高
優先 — 這類文獻是本研究最關心的交集。

**1. Exercise / 運動**
physical activity, exercise, exercise intervention, aerobic exercise intervention,
acute exercise, chronic exercise, exercise prescription, exercise intensity,
exercise dose-response, volume-matched exercise, HIIT, MICT, VO2peak, VO2max,
cardiorespiratory fitness, aerobic fitness, body composition

**2. Emotion / 情緒**
emotion, mood regulation, affective reactivity, feeling, valence, arousal,
depression, anxiety

**3. HRV / 心率變異度**
heart rate variability, HRV, autonomic nervous system, vagal tone

**4. EEG / 腦波**
EEG, resting-state EEG, ERP, brain oscillations, neural oscillations,
frontal alpha asymmetry (FAA), theta/alpha/beta power, power spectral density

**5. Cognition / 認知功能**
cognition, cognitive function, executive function, inhibition, working memory,
cognitive flexibility, planning, attention, processing speed, Yerkes-Dodson law,
inverted-U hypothesis

## Must-have criteria (a paper must clear ALL of these to be ranked High/Medium)
- Human subjects, 一般健康成人或大學生族群(非臨床病人為主的樣本)
- 涉及有氧運動介入(非單次急性運動,至少數週的訓練型介入為主；急性運動研究若與
  下列測量高度相關可放寬)
- 測量至少一項:心肺適能(VO2peak/VO2max)、認知表現/執行功能、靜息態 EEG、HRV、情緒

## Nice-to-have (boosts relevance score)
- 多模態測量(同時涵蓋 EEG + 認知 + HRV 等 2 項以上)
- RCT 或有對照組設計
- 長期性或介入週期 8–16 週(與本研究 12 週貼近)
- 使用 resting EEG 睜眼/閉眼典範
- 使用 tasked EEG
- 使用認知作業

## Exclusions (drop these)
- 純阻力訓練介入且無有氧成分(除非作為對照比較組)
- 動物實驗
- 純綜述無新數據 — 除非是統合分析,可作為未來基準值來源
- 以臨床病人(如失智、憂鬱症診斷族群)為主要樣本,且結果難以類推到一般成人

## Standard benchmarks for comparison
尚未設定基準值 — 目前 digest 只做篩選與相關性排序，不強制附「與領域標竿值比較」表格。
若之後找到合適的統合分析(例如有氧運動介入對執行功能、VO2peak 與認知表現關聯性的
meta-analysis),請把數值與出處填進下表，LitPilot 會自動開始使用它做比較。

| Metric | Field-standard value | Source / context |
|---|---|---|
| (待補) | | |

## Output preferences
- Length: fits on one screen; tables do the heavy lifting
- Tone: plain, 給自己/指導教授看,不要浮誇
- 目前不強制附「與領域標竿值比較」表格(見上方,基準值尚未設定)；等基準值補上後再恢復強制顯示
- Always end with concrete follow-up actions
- Always also produce the Excel comparison workbook and a benchmark chart (see comparison_outputs.md)
- Default cadence: run every Monday at 18:00 (6 PM) with a 7-day window and share the update
- 找到候選文獻後,先用 Crossref 補齊 DOI、ISSN、期刊全名,再繼續其他欄位
- Excel 工作表與圖表存到 `D:\claudecode\research_ting\litpilot\`(永久資料夾,不要只放在暫存目錄),檔名格式沿用 `LitPilot_<主題>_<YYYY-MM-DD>`
