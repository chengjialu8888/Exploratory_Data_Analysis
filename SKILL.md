---
name: eda
description: Exploratory Data Analysis for CSV, Excel, TSV, and tabular data files. Use when the user asks Codex to inspect, profile, analyze, visualize, summarize, or find insights in spreadsheet-like datasets; produce Markdown reports, Feishu/Lark documents, Mermaid diagrams, or htmlbox interactive visualizations; discover data quality issues, distributions, relationships, anomalies, segments, or fun facts; or recommend next analysis directions.
---

# Exploratory Data Analysis

Use this skill to explore tabular data through an adaptive Code-Act loop: make a short plan, execute Python analysis, observe results, revise the questions, and repeat until the dataset is understood well enough to report.

Prefer evidence over narrative. Every meaningful claim in the final report should be backed by a computed statistic, row count, rate, example record, or chart.

## Progress Updates

For long-running EDA tasks, especially when loading large files, spawning independent analysis passes, generating Feishu/Lark documents, or creating htmlbox charts, provide progress updates about every 10 minutes. Each update should:
- say what has been completed
- recalibrate the user's expectation for what remains
- call out potential blockers early, such as file parsing issues, missing permissions, slow chart verification, ambiguous schema, or data-quality problems that may change the analysis plan

If the user includes this prompt, treat it as an explicit progress contract: "每 10 分钟给我汇报进展，帮我校准预期，提前知会潜在卡点".

## Core Tools

- Use Python with `pandas`, `numpy`, and standard plotting/statistics libraries available in the environment.
- Read CSV/TSV with `pandas.read_csv`; read Excel with `pandas.read_excel`.
- Save intermediate normalized data to a local file when it helps parallel subtasks share the same input.
- Use Mermaid diagrams in Markdown for compact visual summaries. Use tables for exact numbers when Mermaid would be awkward.
- When the user asks for a Feishu/Lark deliverable, create a document report and use htmlbox visualizations for charts that benefit from interaction, animation, or ECharts.

## Workflow

### Phase 0: Load And Normalize

1. Locate the dataset file from the user's request or workspace.
2. Load it with pandas, trying likely encodings and separators when needed.
3. Preserve the original file. Write any cleaned working copy to a new temporary or clearly named derived file.
4. Capture a short data dictionary:
   - shape, columns, dtypes, memory footprint
   - sample rows
   - likely identifiers, dates, categories, numeric measures, text fields
   - parsing assumptions and any load warnings

### Phase 1: Data Quality Gate

Run this gate serially before deeper analysis. Do not spawn subtasks until the basic dataset contract is clear.

Check:
- missingness by column and row
- duplicate rows and duplicate candidate IDs
- constant or near-constant columns
- type mismatches, parse failures, mixed units, suspicious sentinels
- numeric ranges, impossible values, negative values where unexpected
- date coverage, gaps, timezone or granularity issues
- categorical cardinality, rare levels, inconsistent labels

If the file cannot be loaded or the schema is too ambiguous, stop and report the blocker with the exact error and the smallest useful next step.

### Phase 2: Parallel Exploration

For broad datasets, spawn independent subtasks when they can run without depending on each other's intermediate conclusions. In Codex, use available multi-agent or parallel task tools when present; otherwise emulate the same separation by running independent analysis passes and keeping outputs isolated.

Good fan-out tasks:
- Univariate profiling: distributions, summary stats, category frequencies, date coverage.
- Bivariate and multivariate relationships: correlations, grouped comparisons, cross-tabs, regression-style sanity checks.
- Segmentation: cohorts, clusters, top/bottom groups, time windows, geography/product/user splits.
- Anomaly and outlier detection: unusual rows, spikes, gaps, impossible combinations, leverage points.
- Fun facts mining: surprising, counterintuitive, memorable, or highly specific findings.

Give each spawned subtask a precise contract:
- goal: what to investigate
- boundary: what not to change or assume
- input: path to the source or normalized file, plus schema notes from Phase 1
- output: concise Markdown with computed evidence, caveats, and 1-3 Mermaid-ready chart ideas

Avoid parallelism when subtasks require real-time coordination, share mutable files, or depend on each other's conclusions.

### Phase 3: Fan-In And Synthesis

Combine subtask outputs. Resolve contradictions by rerunning the relevant calculation in the main context. Prefer a smaller set of strong insights over a long catalog of weak observations.

Read `references/report-contract.md` and `references/output-formats.md` before drafting the final report.

The final deliverable should be a Markdown report that:
- starts with the dataset's basic facts and the most important caveats
- adapts sections to the dataset instead of following a rigid template
- includes Mermaid diagrams when they clarify structure, distributions, flows, or relationships
- includes fun facts only when they are supported by numbers
- recommends concrete next analysis directions

If the user did not specify an output target, default to a local Markdown report. If the user asks for "Feishu", "Lark", "doc", "document", "interactive chart", "dashboard", "htmlbox", "ECharts", or "visual report", choose the matching option from `references/output-formats.md`.

## Analysis Standards

- Be explicit about uncertainty, sample size, missing data, and possible data leakage.
- Do not infer causality from correlations.
- Do not silently drop rows or impute values without reporting the rule and impact.
- For personally identifiable or sensitive data, aggregate by default and avoid exposing raw personal records unless the user explicitly needs row-level debugging.
- Keep code reproducible enough that another Codex run can rerun the checks.

## Mermaid Guidance

Use Mermaid for:
- schema and column-role maps
- analysis flow diagrams
- category share pie charts
- time trend line charts when there are a few clean series
- relationship summaries or decision trees

Do not force Mermaid for dense scatterplots, many-category histograms, or high-cardinality heatmaps. For those, use Markdown tables or saved image files if plotting is available.

## Feishu/Lark Htmlbox Guidance

Use Feishu/Lark documents for shareable executive reports. Use htmlbox blocks when the report needs interactive or animated charts inside the document.

Good htmlbox candidates:
- time-series trends, multi-series lines, bar charts, stacked bars, and bar races
- scatter/bubble plots, heatmaps, calendar heatmaps, boxplots, and parallel coordinates
- funnels, Sankey diagrams, treemaps, sunbursts, gauges, and KPI dashboards
- relationship graphs, flow diagrams, and animated process/status views

For every htmlbox chart:
- generate a self-contained HTML file from computed aggregate data, never from private raw rows unless necessary
- use the `feishu-cli-htmlbox` workflow and chart recipes
- run the local verification script before inserting into a document
- inspect the screenshot for blank charts, broken CDN loads, bad labels, or misleading axes
- insert the verified HTML with `feishu-cli doc htmlbox create <doc_id> --html-file <file>`
- alternate document headings and htmlbox blocks so the report remains navigable
