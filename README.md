**Authors:** Tien Dat Nguyen$^{1,2}$, Duy Linh Vu Nguyen$^{1,2}$, Gia Kiet Le$^{1,2}$, Minh Nhut Nguyen$^{1,2}$, Dinh Thuan Nguyen$^{1,2,*}$
<br>
$^{1}$*University of Information Technology, VNU-HCM, Vietnam*
<br>
$^{2}$*Vietnam National University, Ho Chi Minh City, Vietnam*

---

## Overview

This repository contains the implementation and experimental data for our paper: **"Enhancing Text-to-Cypher Capabilities of Small Language Models via Draft-Guided Schema Linking and Self-Correction"**.

We present a **training-free enhancement pipeline** designed to improve the performance of Small Language Models (SLMs) like Qwen2.5 and Gemma-2 in translating natural language to Cypher queries. Our approach addresses the challenges of schema complexity and graph-specific constraints (e.g., relationship directionality, variable-length paths) in resource-constrained environments.

### Key Features
* **Draft-based Schema Linking:** dynamically extracts relevant sub-schemas to reduce context window usage and hallucinations.
* **Execution-driven Self-Correction:** An iterative feedback loop that executes queries against Neo4j.
* **Automatic Chain-of-Thought Hints:** Parses database error messages into natural language diagnostic prompts without requiring additional LLM calls.
* **Resource Efficiency:** Achieves significant accuracy gains on consumer-grade hardware without fine-tuning.

---

## Methodology

Our workflow consists of two main phases:

1.  **Schema Linking:** The model generates a "draft query" based on the user question. We use regex to extract relevant nodes/relationships from this draft to form a focused sub-schema.
2.  **Self-Correction Loop:**
    * Generate Cypher query using the sub-schema.
    * Execute against Neo4j.
    * If error: Analyze error -> Generate specific hint (e.g., *"Check relationship direction"*) -> Re-prompt model.
    * Repeat until success or max retries.

---

## Main Results

Evaluated on the **Text2Cypher-2024v1** benchmark.

| Method | Model | Exec. Acc (%) | Syntax Error (%) |
| :--- | :--- | :---: | :---: |
| Baseline | Qwen2.5-7B-Instruct | 20.84 | 25.6 |
| **Our Workflow** | **Qwen2.5-7B-Instruct** | **29.14 (+8.3%)** | **7.3 (-18.3%)** |
| Baseline | Qwen2.5-Coder-14B | 37.07 | 13.6 |
| **Our Workflow** | **Qwen2.5-Coder-14B** | **40.91 (+3.8%)** | **3.2 (-10.4%)** |

> **Key Insight:** While our workflow significantly boosts base models, we observed a trade-off for fine-tuned specialists, where dynamic schema filtering may conflict with memorized schema patterns.

---
