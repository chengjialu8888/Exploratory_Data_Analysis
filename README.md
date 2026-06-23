# Exploratory Data Analysis Skill

![Exploratory Data Analysis concept](assets/eda-concept.png)

**A Codex skill for adaptive exploratory data analysis over CSV, Excel, TSV, and spreadsheet-like datasets.**

This skill turns a raw tabular file into an evidence-backed Markdown report. It uses a Code-Act loop rather than a rigid checklist: plan briefly, run Python analysis, observe the results, ask better questions, and synthesize the strongest findings.

## Why This Exists

Exploratory Data Analysis is not just "print `df.describe()`." Good EDA finds the shape of a dataset, catches quality traps, notices unexpected relationships, and explains what to investigate next. This skill gives Codex a reusable operating procedure for doing that work consistently.

## What It Does

- Loads CSV, TSV, Excel, and other tabular data with pandas.
- Builds a quick data dictionary: shape, types, sample rows, likely identifiers, dates, categories, measures, and text fields.
- Runs a serial data-quality gate before deeper analysis.
- Uses independent analysis passes for distributions, relationships, segments, anomalies, and fun facts when the task is broad enough.
- Produces a Markdown report with Mermaid diagrams where they help.
- Requires claims to be supported by computed evidence: counts, denominators, percentages, ranges, examples, or caveats.

## Architecture

![EDA execution architecture](assets/eda-architecture.png)

The workflow is intentionally split into serial gates and parallel exploration:

1. **Load and normalize** the source file without mutating the original.
2. **Audit data quality** before drawing conclusions.
3. **Fan out** independent exploration passes when they do not depend on each other.
4. **Fan in** by recomputing headline numbers, resolving contradictions, and writing one adaptive report.

## Install

Clone or download this repository into your Codex skills directory:

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/chengjialu8888/Exploratory_Data_Analysis.git ~/.codex/skills/eda
```

Restart Codex so the skill metadata is reloaded.

## Use

Ask Codex something like:

```text
Use $eda to explore ./data/customers.xlsx and produce a Markdown report with Mermaid visuals.
```

Or:

```text
Use $eda on this CSV. I care most about missing values, segment differences, anomalies, and surprising findings.
```

## Output

The final report should adapt to the dataset, but typically includes:

- Executive summary
- Dataset basics
- Data-quality notes
- Key patterns
- Relationships and segments
- Anomalies and edge cases
- Fun facts backed by numbers
- Recommended next steps
- Appendix with methods and assumptions

See [`references/report-contract.md`](references/report-contract.md) for the report interface and subtask output contract.

## Repository Layout

```text
.
├── SKILL.md                       # Main Codex skill instructions
├── references/
│   └── report-contract.md         # Final report and subtask contracts
├── agents/
│   └── openai.yaml                # UI metadata
├── assets/
│   ├── eda-concept.svg/png        # Philosophy header visual
│   └── eda-architecture.svg/png   # Workflow architecture visual
└── .github/                       # Issue and PR templates
```

## Design Principles

- **Evidence before prose**: every important conclusion should point back to a computed result.
- **Adaptive, not templated**: the report follows the dataset, not a fixed dashboard layout.
- **Quality gate first**: missingness, duplicates, parse failures, and suspicious values are checked before deep analysis.
- **Parallel only when independent**: use fan-out for low-coupling exploration, then fan-in for verification.
- **Privacy by default**: aggregate sensitive data unless row-level inspection is explicitly needed.

## Contributing

Contributions are welcome. Start with [`CONTRIBUTING.md`](CONTRIBUTING.md), and avoid committing private datasets or generated reports that contain sensitive information.

## License

MIT
