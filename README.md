CausalSim-Enterprise 🚀

LLM Causal Reasoning & Safety Evaluation from CSV

Author: Youla Yang (Indiana University Bloomington)

1. Overview

CausalSim-Enterprise is a fully reproducible evaluation pipeline for assessing causal reasoning stability and safety of large language models (LLMs) — including GPT-4o, Mistral, Gemma, Llama, and Qwen — across enterprise-level causal inference tasks.

It performs three main operations:

Load causal scenario CSVs (each row is a real-world business or risk-management “A→B or B→A?” question).

Query multiple models via OpenRouter API, under two settings:

Baseline – normal causal world

Counterfactual – “flipped” causal world where one causal path is disabled

Compute and visualize evaluation metrics, including:

Accuracy

Safe Consistency Rate (CSR)

Causal Advantage Index (CAI)

Enterprise Risk Score (ERS-Sim)

Cross-model consistency heatmaps

The goal: identify which LLMs are safe and reliable in enterprise causal reasoning under stress-tested conditions.

2. Key Features

✅ Colab-friendly one-click pipeline – just upload your CSV, paste your API key, and run all cells.

✅ Automatic prompt generation – parses scenario text into two causal hypotheses (A→B and B→A).

✅ Batch evaluation across multiple models – loops through models and handles retries gracefully.

✅ Structured answer parsing – enforces model responses with:

Answer: A or B
Confidence: <0-100>%
Justification: ...


✅ Enterprise Risk Scoring (ERS-Sim) – quantifies instability in high-risk scenarios.

✅ Full export support – results, metrics, and plots are saved as both CSV and PNG, bundled in final_outputs.zip.

3. Input Format

Input comes from a CSV with the following columns:

Column	Description
scenario_id	Unique ID for each scenario
scenario_text	Text description of the causal dilemma (“Does A cause B or B cause A?”)
y_true	Ground-truth label ("A" or "B")
w_i	Scenario risk weight (higher = higher enterprise impact)
4. Environment Setup

Dependencies (installed automatically in the notebook):

!pip install openai tqdm pandas numpy matplotlib -q


Required libraries:

openai (via OpenRouter client)

pandas, numpy, tqdm, matplotlib

google.colab (for file upload & download in Colab)

Set your API key in the first cell:

import os
os.environ["OPENROUTER_API_KEY"] = "sk-..."  # your key here

5. Evaluation Pipeline
Step 0 – Upload & Preprocess

Upload scenarios.csv

Each scenario is parsed into:

context, hypotheses A/B, ground truth, risk weight

Auto-build two prompts:

baseline_prompt – normal world

cf_prompt – counterfactual world

Step 1 – Multi-Model Inference

For each model in MODEL_LIST, run both prompts, extract:

baseline_answer, baseline_conf

cf_answer, cf_conf

textual justification

Results are saved as:

results_<provider>_<model>.csv

Step 2 – Metric Computation

The notebook computes:

Accuracy – baseline correctness.

CSR (Causal Stability Rate) – whether the model updates logically under counterfactuals.

ERS-Sim – weighted risk score penalizing unstable, overconfident flips in high-impact scenarios.

CAI (Causal Advantage Index) – difference between causal and anti-causal accuracy.

SafeConsistency – percentage of cases correct in both baseline & counterfactual settings.

RawConsistency – whether baseline vs counterfactual answers agree (regardless of correctness).

Summaries are saved to:

summary_all_models.csv

Step 3 – Risk Weight Merge

Automatically merges w_i (risk weights) into all result files if missing.

Step 4 – Aggregate Analysis

Combines all metrics across models into:

final_analysis_summary.csv

Step 5 – Visualization

Generates three high-resolution figures:

Figure	Description	Output
1	Causal vs Anti-Causal Accuracy (ΔCAI)	fig1_causal_vs_anti_accuracy.png
2	Accuracy vs SafeConsistency Scatter	fig2_accuracy_vs_safeconsistency.png
3	Cross-Model Consistency Heatmap	fig3_consistency_heatmap.png
Step 6 – Packaging

All results and figures are zipped into:

final_outputs.zip


Automatically downloadable in Colab.

6. Recommended Repo Structure
.
├── README.md
├── CausalSim-Enterprise.ipynb
├── scenarios.csv
├── results_run/
│   ├── results_openai_gpt-4o-mini.csv
│   ├── results_mistralai_mixtral-8x7b.csv
│   ├── results_llama-3.1-8b.csv
│   ├── final_analysis_summary.csv
│   ├── fig1_causal_vs_anti_accuracy.png
│   ├── fig2_accuracy_vs_safeconsistency.png
│   ├── fig3_consistency_heatmap.png
└── final_outputs.zip

7. Reproduction (Colab Workflow)

Open the notebook in Google Colab.

Paste your OPENROUTER_API_KEY.

Install dependencies.

Upload scenarios.csv.

Run all cells sequentially.

Download final_outputs.zip for ready-to-share results.

8. Metrics Explained

CAI (Causal Advantage Index)
CAI = Accuracy_causal - Accuracy_anti
→ measures directional bias. High CAI = strong preference for causal direction A→B.

SafeConsistency
Fraction of samples correctly answered in both baseline and counterfactual.
High = robust reasoning.

ERS-Sim (Enterprise Risk Score)
Weighted penalty for inconsistent, overconfident predictions in high-risk contexts.
Lower = safer, more reliable model.

9. License & Attribution

This repository contains inference outputs from multiple commercial LLMs (e.g., GPT-4o-mini).
Before redistributing, ensure compliance with each model provider’s terms of service.

If you use this repository in research, please cite:

Youla Yang, Indiana University Bloomington (Luddy School of Informatics, Computing, and Engineering).

10. TL;DR

Upload a CSV of causal enterprise scenarios →
Ask multiple LLMs under baseline & counterfactual conditions →
Collect structured answers →
Compute safety and stability metrics →
Plot results →
Zip everything up.

“Evaluation as evidence.”
A single notebook to expose hidden causal instability in enterprise LLMs.
