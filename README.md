# Research Meeting Notes

**Participants:** Tiange Lyu (TG Lyu), Zheng-Lin Lin (ZL Lin), Anni Wang, Yiting Tong
**Project:** Knowledge Conflict Resolution and Evidence Preferences in Decision-Oriented and General-Purpose Language Models  
**Primary Focus:** Jev & OpenAI Decisions API vs. General-Purpose LLMs  
**Target:** Initial arXiv submission by mid-December 2026

---

## 1. Research Objectives

This study aims to investigate how **Decision-Oriented Models and General-Purpose LLMs** make judgments, resolve conflicts, and exhibit evidence preferences and confidence changes when confronted with contradictory knowledge and evidence.

The research primarily focuses on **Jev and the OpenAI Decisions API**, with General-Purpose LLMs serving as the main comparison group. The objective is to identify and analyze behavioral differences between these two model categories in Knowledge Conflict Resolution.

The study distinguishes between two types of knowledge conflict:

1. **Parametric–Contextual Conflict:** A conflict between a model's existing parametric knowledge and newly provided external evidence.
2. **Contextual–Contextual Conflict:** A conflict between two external information sources presenting contradictory claims about the same event.

### Core Research Questions

1. **Conflict Resolution:** Can models identify and correctly resolve conflicts between contradictory knowledge and evidence?
2. **Evidence Preferences:** Do models tend to favor their existing knowledge, external evidence, more recent information, or particular information sources?
3. **Order & Source Sensitivity:** How do evidence presentation order and source credibility influence model judgments?
4. **Confidence Dynamics:** How do knowledge conflicts affect model confidence, and do confidence changes reflect decision correctness?
5. **Model Type Comparison:** Are there systematic differences between Decision-Oriented Models and General-Purpose LLMs in their conflict resolution capabilities, evidence preferences, and decision reliability?

---

## 2. Models & API

### 2.1 Decision-Oriented Models

| Model | Role | Input Cost / 1M Tokens |
|---|---|---:|
| Jev | Primary Decision Model | $0.042 |
| OpenAI Decisions API | Primary Decision Model | $0.100 |

**API-based probing will be prioritized.**

Pricing references:

- [Jev Pricing](https://hackmd.io/_uploads/rym9W9ihGe.png)
- [OpenAI Decisions API Pricing](https://hackmd.io/_uploads/Hy62gqonGl.png)
- [API Comparison](https://huggingface.co/blog/a2aprotocol/openai-decisions-api-vs-jev-a-practical-guide)

### 2.2 General-Purpose LLMs

| Model | Role |
|---|---|
| Qwen-3.8 27B | Open-weight comparison model |
| Llama 3 | Open-weight comparison model |
| Claude Haiku 5.5 | General-purpose API comparison model |

General-Purpose LLMs will serve as the **primary comparison group**, rather than merely providing supplementary accuracy baselines.

Both model categories will be evaluated using identical events, evidence, and conflict conditions to enable direct comparisons of their decision-making behavior and evidence preferences.

The final model selection and specific versions remain to be confirmed. Since different APIs may provide confidence outputs in different formats, the evaluation methodology must ensure fair and meaningful comparisons.

---

## 3. Dataset Construction

### 3.1 Dataset Specification

**Total: 150 Real-World Events**

| Dataset | Size | Event Date | Purpose |
|---|---:|---|---|
| Historical Events | 100 | Before 2026 | Conflicts between existing knowledge and external evidence |
| Novel Events | 50 | After September 29, 2026 | Multi-source knowledge conflicts involving recent events |

- **Pilot Dataset:** Approximately 30 events (20 Historical + 10 Novel)
- **Event Discovery:** Polymarket API / Kalshi API
- **Ground Truth:** Independently verified authoritative sources
- **Format:** True–False statement pairs or multiple statements

### 3.2 Temporal Design

**Historical Events (100 Events)**

Select events that occurred before 2026 and have clearly established outcomes.

The primary focus is on **Parametric–Contextual Conflict**, including:

- How models behave when their existing knowledge contradicts external evidence.
- Whether models prefer their existing knowledge or newly provided information.
- Whether Decision-Oriented Models and General-Purpose LLMs exhibit different tendencies in relying on prior knowledge.

A No-Evidence Baseline will be used to examine models' initial judgments and determine whether they demonstrate observable prior knowledge of the events.

**Novel Events (50 Events)**

Select recent events occurring after September 29, 2026. This date serves as the study's temporal boundary and should not automatically be interpreted as the knowledge cutoff of every evaluated model.

The primary focus is on **Contextual–Contextual Conflict**, including:

- How models handle contradictory evidence when they lack prior knowledge of an event.
- How models select between information from different sources.
- Whether Decision-Oriented Models and General-Purpose LLMs exhibit different evidence preferences and confidence dynamics.

The actual knowledge cutoff of each model must be verified. Potential information leakage through external retrieval or subsequent model updates must also be controlled.

### 3.3 Event Categories

| Domain | Examples | Ground Truth Sources |
|---|---|---|
| Economics | CPI, GDP, interest rates, unemployment rates | BLS, BEA, Fed, ECB |
| Geopolitics | Elections, diplomatic agreements, sanctions | Official government sources |
| Technology & Science | AI releases, space missions | Official announcements, NASA |
| Corporate Finance | Financial reports, mergers and acquisitions | SEC filings, corporate announcements |
| Geography & Environment | Earthquakes, extreme weather events | USGS, meteorological agencies |

### 3.4 Ground Truth Strategy

**Prediction Markets are used for Event Discovery, NOT Ground-Truth Determination.**

Polymarket and Kalshi will primarily be used to identify relevant events and collect event-related questions. Final ground truth will be established through independent verification using authoritative external sources.

Dataset construction pipeline:

1. Collect events that meet the temporal criteria through the Polymarket and Kalshi APIs.
2. Verify the ground truth using official or authoritative sources.
3. Exclude events without clearly established outcomes or those involving substantial ambiguity.
4. Construct correct and incorrect statements and corresponding evidence.
5. Preserve event dates, information sources, publication timestamps, and verification records.

Final UMA resolutions will not be treated as objective ground truth by default. Events that enter UMA's formal dispute resolution process may be excluded or explicitly flagged. However, even undisputed events must undergo independent verification.

---

## 4. Experimental Design

### 4.1 Knowledge Conflict Probing

For each event, construct correct evidence A and incorrect evidence B. Decision-Oriented Models and General-Purpose LLMs will be evaluated under identical experimental conditions.

| Condition | Description |
|---|---|
| **Baseline** | Present only the question, without any external evidence |
| **Evidence A** | Provide only correct evidence supporting the true outcome |
| **Evidence B** | Provide only incorrect evidence supporting the opposite outcome |
| **Conflict A+B** | Present both correct evidence A and incorrect evidence B |
| **Conflict B+A** | Present the same evidence in the reverse order |
| **Credibility Conflict** | Present contradictory evidence accompanied by information about differences in source credibility |

### 4.2 Evidence Preferences & Model Behavior

The study will primarily examine models' behavioral preferences when confronted with knowledge conflicts:

- **Parametric Knowledge Preference:** Do models tend to preserve their existing knowledge while disregarding external evidence?
- **Contextual Evidence Preference:** How susceptible are models to newly provided information?
- **Source Preference:** Do models favor particular sources, or can they appropriately distinguish between sources of different reliability?
- **Order Preference:** Do models systematically favor evidence presented earlier or later?
- **Confidence Behavior:** How does model confidence change before and after exposure to conflicting evidence, and are these changes justified?

The emphasis is on comparing behavioral patterns across model categories rather than evaluating final-answer accuracy alone.

### 4.3 Primary Evaluation Metrics

| Metric | Purpose |
|---|---|
| Conflict Resolution Accuracy | Measures whether models select the correct outcome when presented with contradictory evidence |
| Evidence Preference / Sensitivity | Measures models' preferences for and responsiveness to different evidence |
| Order Sensitivity | Measures the effect of evidence presentation order on decisions |
| Source Credibility Sensitivity | Measures the influence of source reliability on model judgments |
| Confidence Change | Measures changes in model confidence before and after exposure to conflicting evidence |
| Cross-Model Behavioral Differences | Compares behavioral differences between Decision-Oriented Models and General-Purpose LLMs |

**Primary comparison dimensions:**

1. Decision-Oriented Models vs. General-Purpose LLMs
2. Jev vs. OpenAI Decisions API
3. Historical vs. Novel Events
4. Individual Evidence vs. Conflicting Evidence

To ensure fair comparisons, prompts, answer choices, evidence content, and presentation order should be carefully controlled.

If models provide confidence outputs in different formats, the evaluation should distinguish between directly comparable decision metrics and probabilistic metrics requiring additional calibration.

### 4.4 Secondary Evaluation: Confidence Calibration

Additional evaluation dimensions include:

- Accuracy
- Confidence vs. Correctness
- Calibration Error
- Decision Threshold
- Selective Accuracy / Abstention (if supported by the model interface)

This analysis will investigate whether knowledge conflicts create discrepancies between reported confidence and actual accuracy, and whether different model categories exhibit distinct calibration characteristics.

Confidence Calibration will serve as a supplementary analysis for evaluating the reliability of Knowledge Conflict Resolution.

---

## 5. Related Work

### Jev Benchmark Evaluation

[arXiv:2610.11978](https://arxiv.org/abs/2610.11978)

This work compares Jev with other models across general knowledge, reasoning, and multilingual capabilities.

**Our Distinction:** Rather than focusing on accuracy in general benchmark evaluations, our study investigates differences between model categories in their decision-making capabilities and evidence preferences under knowledge conflicts.

### Beyond Answer Confidence: A Controlled Audit of Self-Knowledge in a Black-Box Decision Model

[arXiv:2610.01006](https://arxiv.org/abs/2610.01006)

This study primarily investigates Jev's self-knowledge, knowledge boundaries, and confidence calibration. It also includes experiments involving evidence interventions and contradictory information.

**Our Distinction:**

- Knowledge Conflict Resolution and Evidence Preferences are the primary research questions.
- We systematically control correct evidence, incorrect evidence, and simultaneously presented contradictory evidence.
- We analyze evidence presentation order, source credibility, and models' preferences for their existing knowledge.
- We compare conflict resolution behaviors across Historical and Novel Events.
- We place behavioral differences between Decision-Oriented Models and General-Purpose LLMs at the center of the analysis.

Additional references: [Literature Collection](https://hackmd.io/@chrizeroxtwo/B1jjI5o2fx)

---

## 6. Next Steps

**Target: Mid-December 2026 arXiv Submission**

| Priority | Task |
|---|---|
| 1 | Confirm model versions, API access methods, and output formats for Jev, OpenAI Decisions API, and General-Purpose LLMs |
| 2 | Construct a 30-event Pilot Dataset (20 Historical + 10 Novel) |
| 3 | Implement the six evidence conditions and conduct preliminary probing experiments |
| 4 | Verify ground truth and validate evidence materials and experimental conditions |
| 5 | Expand the dataset to 100 Historical + 50 Novel Events |
| 6 | Conduct cross-model Knowledge Conflict Probing |
| 7 | Analyze Evidence Preferences, Conflict Resolution, and Confidence Dynamics |
| 8 | Consolidate research findings and prepare the initial arXiv manuscript |

---

## Preliminary Research Contribution

**A Controlled Comparative Study of Knowledge Conflict Resolution and Evidence Preferences in Decision-Oriented and General-Purpose Language Models.**

This study systematically compares the conflict resolution capabilities and evidence preferences of Decision-Oriented Models and General-Purpose LLMs through a temporally controlled dataset of real-world events, independently verified ground truth, and six controlled evidence conditions.

The anticipated research contributions include:

1. **Conflict Resolution:** A systematic comparison of the decision-making capabilities of both model categories when confronted with contradictory knowledge and evidence.
2. **Evidence Preferences:** An analysis of how models prioritize existing knowledge, newly provided evidence, source credibility, and evidence presentation order.
3. **Temporal Comparison:** A comparison of knowledge conflict behaviors across Historical and Novel Events.
4. **Confidence Dynamics:** An investigation of how model confidence changes under conflicting evidence and whether these changes reliably reflect decision correctness.
5. **Cross-Model Comparison:** An examination of whether specialized decision models and general-purpose LLMs exhibit systematic and reproducible differences in knowledge conflict resolution behavior.

### Core Research Question

*How do decision-oriented models and general-purpose LLMs differ in their ability to resolve knowledge conflicts, their preferences for competing evidence, and their confidence when confronted with contradictory information?*
