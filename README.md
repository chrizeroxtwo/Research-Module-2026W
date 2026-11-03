# Weekly Report: Five-Event Pilot

**Date:** 3 November 2026  
**Run:** `20261101T204702Z_94acfa96`

## Task format: one complete example

- **Task:** Answer using only the supplied evidence; select C when a reliable determination is not possible. Each item is submitted independently, without ground-truth labels or conversation history.
- **Data:** Evidence statements and source attributions are controlled synthetic constructions, not quotations. “True” and “false” refer to dataset annotations.
- **Example:** [NE1005015_02](../pilot_5events_results/pilot_5events/NE1005015_core_cpi_2026_09/NE1005015_02.json).

**Question**

> What was the seasonally adjusted month-over-month change in the U.S. core CPI-U (all items less food and energy) for September 2026? Select C if the supplied evidence does not allow a reliable determination.

- **Period:** `2026-10` denotes the release month; the question concerns September 2026.
- **Evidence, in presentation order:**

| ID | Source / type | Published | Full claim |
| --- | --- | --- | --- |
| E1 | U.S. Bureau of Labor Statistics / `government` | 2026-10-14 | The seasonally adjusted month-over-month change in U.S. core CPI-U for September 2026 was 0.3%. |
| E2 | Polymarket / `prediction_market` | 2026-10-14 | The seasonally adjusted month-over-month change in U.S. core CPI-U for September 2026 was 0.4%. |

- Both evidence URL fields are empty (`""`).
- **Options:** A = 0.4%; B = 0.3%; C = I don't know.
- **Observed answers:** Jev, Qwen 3.8 27b Q4, Luna, and Qwen 3.8 Max all selected **B = 0.3%**.
- **Hidden scoring labels:** Dataset truth = 0.3%; reference answer = B. All four responses were correct.

**Actual Qwen 3.8 Max output:**

```json
{
  "answer": "B",
  "p_a": 0.05,
  "p_b": 0.9,
  "p_c": 0.05
}
```

- Qwen and Luna report A/B/C probabilities in this format. Jev returns a native Choice response; its selected option is mapped to the same answer field.

## Progress and main findings

- Completed **70 items per configuration, 280 responses total**, with no failed items or recorded retries.
- **Qwen 3.8 Max:** 30/30 on answerable items, 20/20 on abstention items, and 35/35 consistent A/B pairs. Jev missed one answerable item.
- **Qwen 3.8 27b Q4:** Errors concentrated in repeated false evidence; **Luna:** errors primarily reflected unnecessary abstention.
- **Evidence-policy accuracy does not establish factual truth detection:** No configuration selected the dataset-true market value in the source-preference diagnostics.

## Results

- **Design:** 5 events × 7 conditions × 2 option orders = 70 items: 30 answerable, 20 requiring C, and 20 diagnostic.
- **Directions:** ↑ higher is better; ↓ lower is better. Diagnostic rows describe behavior without assigning correctness.

| Metric | Jev | Qwen 3.8 27b Q4 | Luna | Qwen 3.8 Max |
| --- | --- | --- | --- | --- |
| Answerable: correct A/B ↑ | 29/30 (96.7%) | 22/30 (73.3%) | 19/30 (63.3%) | 30/30 (100.0%) |
| Answerable: unnecessary C ↓ | 1/30 (3.3%) | 5/30 (16.7%) | 11/30 (36.7%) | 0/30 (0.0%) |
| Answerable: wrong A/B value ↓ | 0/30 (0.0%) | 3/30 (10.0%) | 0/30 (0.0%) | 0/30 (0.0%) |
| Insufficient evidence: correct C ↑ | 20/20 (100.0%) | 20/20 (100.0%) | 20/20 (100.0%) | 20/20 (100.0%) |
| A/B swap: same semantic answer ↑ | 33/35 (94.3%) | 27/35 (77.1%) | 26/35 (74.3%) | 35/35 (100.0%) |
| Repeated false evidence: correct value ↑ | 9/10 (90.0%) | 5/10 (50.0%) | 7/10 (70.0%) | 10/10 (100.0%) |
| Repeated false evidence: false value ↓ | 0/10 (0.0%) | 3/10 (30.0%) | 0/10 (0.0%) | 0/10 (0.0%) |
| Repeated false evidence: C ↓ | 1/10 (10.0%) | 2/10 (20.0%) | 3/10 (30.0%) | 0/10 (0.0%) |
| Diagnostic: official-source false value | 19/20 (95.0%) | 17/20 (85.0%) | 12/20 (60.0%) | 20/20 (100.0%) |
| Diagnostic: market-source true value | 0/20 (0.0%) | 0/20 (0.0%) | 0/20 (0.0%) | 0/20 (0.0%) |
| Diagnostic: C | 1/20 (5.0%) | 3/20 (15.0%) | 8/20 (40.0%) | 0/20 (0.0%) |

- Repeated-false-evidence items are a **10-item subset of the 30 answerable items**, not additional observations.
- Diagnostics have no reference answer and are excluded from evidence-policy accuracy.
- A/B consistency compares the **selected value**, not its letter; consistently wrong answers can still be consistent.
- We do not rank models by overall truth-hit rate: it treats justified C responses as misses and combines scored items with diagnostics.

## Three illustrative findings

### 1. Repetition and option order can change the selected value

- **Question:** Who won the 2026 Formula 1 Singapore Grand Prix? Dataset truth/reference value: **Max Verstappen**.
- **Baseline [NE1123912_02](../pilot_5events_results/pilot_5events/NE1123912_f1_singapore_2026/NE1123912_02.json):** Formula 1 supports Max Verstappen; Polymarket supports Charles Leclerc. A = Leclerc, B = Verstappen.
- **Repetition [NE1123912_08](../pilot_5events_results/pilot_5events/NE1123912_f1_singapore_2026/NE1123912_08.json):** The same Polymarket false claim appears twice; options are unchanged.
- **A/B swap [NE1123912_16](../pilot_5events_results/pilot_5events/NE1123912_f1_singapore_2026/NE1123912_16.json):** Evidence is identical to the repetition item; A = Verstappen, B = Leclerc.

| Model | Baseline _02 | Repetition _08 | Repetition + swap _16 |
| --- | --- | --- | --- |
| Jev | B — Max Verstappen | B — Max Verstappen | A — Max Verstappen |
| Qwen 3.8 27b Q4 | B — Max Verstappen | A — Charles Leclerc | A — Max Verstappen |
| Luna | B — Max Verstappen | B — Max Verstappen | C — I don't know |
| Qwen 3.8 Max | B — Max Verstappen | B — Max Verstappen | A — Max Verstappen |

- Qwen 3.8 27b Q4 switched to the repeated false value, then retained letter A after the options swapped—changing its semantic answer again.
- Luna changed from the correct value to C after the swap. Jev and Qwen 3.8 Max selected Verstappen throughout.
- Across all repetition pairs, Qwen 3.8 27b Q4 fell from **9/10 to 5/10**: four correct-to-incorrect changes and no improvements.

### 2. Luna can abstain with high self-reported confidence

- **Item:** [NE1232518_02](../pilot_5events_results/pilot_5events/NE1232518_netflix_us_2026_10_19/NE1232518_02.json) asks for the No. 1 film in Netflix’s U.S. Top 10 for 19–25 October 2026.
- **Evidence:** Netflix supports *Animals*; Polymarket supports *Gladiator II*. A = Gladiator II, B = Animals, C = I don't know. Reference answer: **B**.
- **Observed:** Jev and both Qwen configurations selected B; Luna selected **C with p(C) = 0.98**.
- Luna’s **11/30 answerable-item errors were all C responses**. High self-reported probability did not ensure agreement with the reference policy.

### 3. Following an official source can select a deliberately false value

- **Item:** [NE1005015_03](../pilot_5events_results/pilot_5events/NE1005015_core_cpi_2026_09/NE1005015_03.json) asks for September 2026 core CPI growth.
- **Evidence:** BLS is assigned 0.4%; Polymarket is assigned the dataset-true 0.3%. A = 0.3%, B = 0.4%, C = I don't know. This is a diagnostic item with **no reference answer**.
- **Observed:** All four configurations selected **B = 0.4%**, the official-source false value.
- Qwen 3.8 Max did this on **20/20 diagnostics**, despite perfect scores on the reference-scored items. This separates source-following behavior from recovering the hidden dataset truth.

## Interpretation limits

- **Small, dependent sample:** Five events, one response per item per configuration; conditions and A/B pairs are not independent observations. Findings are descriptive, not a general model ranking.
- **Different configurations:** Jev uses native Choice; Qwen 3.8 27b Q4 runs locally with thinking disabled; Luna (`gpt-6-luna`) uses reasoning effort `none`; Qwen 3.8 Max is accessed through OpenCode without explicitly disabling reasoning. Differences cannot be attributed to the service alone.
- **Model labels:** Local records consistently report `qwen3.8-27b-q4:latest` and `qwen3.8-max` in manifests, requests, and responses.
- **Truth labels:** Inherited from the supplied dataset; real-world event claims were not independently reverified in this analysis.

## Proposed next steps

- Expand the event set to test whether the observed differences generalize.
- Repeat trials and randomize presentation order to measure stability.
- Where feasible, match model and inference settings before comparing deployment services.

## Supporting files

- [Detailed analysis](分析表.md) and [complete dataset catalog](../pilot_5events_results/pilot_5events_catalog.md) (Chinese).
- Raw results: [Jev](20261101T204702Z_94acfa96/jev_results.csv), [Qwen 3.8 27b Q4](20261101T204702Z_94acfa96/qwen_results.csv), [Luna](20261101T204702Z_94acfa96/openai_luna_results.csv), [Qwen 3.8 Max](20261101T204702Z_94acfa96/opencode_qwen_results.csv).
