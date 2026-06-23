# Data Sources And Scale

Use this reference when the input is not a small local file, especially when the user provides a dataset URL, warehouse export, object-store link, Feishu/Lark file link, or a very large event log.

## Input Types

The dataset may arrive as:

- a local CSV, TSV, Excel, Parquet, JSONL, or compressed file
- a Feishu/Lark document, sheet, drive file, or shared download link
- an HTTP(S) dataset link
- an object-store URL
- a database or warehouse query result
- a folder with partitioned files
- a manifest that points to multiple stage-specific datasets

When the input is a link, first identify:

- access method: direct download, authenticated CLI, browser/session, API, database query, or object storage
- file format and compression
- row count or approximate size
- whether the link returns a preview, a sample, a paginated result, or the full dataset
- whether credentials or user authorization are required

If access is blocked, report the exact blocker and ask for the smallest missing permission or export format.

## The 10k Row Rule

Some tools, previews, and spreadsheet-like surfaces cap reads at 10,000 rows. Treat this as a preview limit, not an analysis limit.

Never claim full-population metrics from:

- the first 10,000 rows
- UI preview rows
- default paginated API responses
- spreadsheet views with hidden filters
- a sampled export whose sampling method is unknown

Allowed uses for capped previews:

- schema discovery
- type inference
- example rows
- early data-quality smell tests
- estimating which full aggregation jobs are needed

Required full-population alternatives:

- use chunked reads: `pandas.read_csv(..., chunksize=...)`
- request or download the full export
- query the warehouse with grouped aggregations
- use Parquet/Arrow/DuckDB/Polars for larger-than-memory data
- process partitioned files and combine summaries
- ask for stage-level aggregate tables when row-level data is too large or sensitive

Every report must state whether findings are full-population, sampled, preview-only, or based on aggregates supplied by the user.

## Large Dataset Workflow

For large datasets:

1. Use a preview only to understand schema and data quality risks.
2. Define the analysis grain: user, event, transaction, match, session, creator, team, or stage.
3. Compute full-population counts and denominators first.
4. Build aggregate tables before visualization.
5. Use samples only to inspect individual examples or suspicious records.
6. Store intermediate summaries as CSV/Parquet/JSON where useful.
7. Recompute headline numbers from the full aggregate before finalizing.

Recommended aggregate tables:

- row counts by source partition
- distinct entity counts by stage
- funnel transition table
- missingness by column
- event counts by day/hour/stage
- top categories with counts and share
- numeric summary by segment
- anomaly candidate table with reason codes

## Stage Funnel And Event Review

For live event, campaign, tournament, or activity-review datasets, preserve the full funnel. These analyses are especially vulnerable to invalid subset conclusions because later stages naturally contain fewer participants.

Always compute:

- total entrants or eligible population
- stage entry count
- stage pass count
- stage drop count
- pass rate and drop rate
- cumulative retention from the first stage
- stage-specific and cumulative denominators

Example stage prior:

| transition | expected pattern |
|---|---|
| Audition -> Promotion round | open participation, around 3,000,000 entrants; about 90% eliminated; historical promoted counts around 200,000-300,000, with future estimates possibly 400,000-600,000 |
| Promotion round -> Final | about 10%-20% of promotion-round participants enter final; much smaller data volume |

Use such priors as expectation calibration, not as replacement for measured counts. If actual data deviates strongly, call it out as either a finding or a data-quality concern.

## Reporting Requirements For Large Inputs

Include a "Data Coverage" or "Population And Sampling" note that states:

- source link or source type
- whether full data was available
- whether any row cap, pagination, or sampling was encountered
- actual rows processed
- distinct entities processed
- stages covered
- partitions or date ranges covered
- what was not analyzed

If only a subset was accessible, avoid definitive language. Use wording such as "in the accessible preview", "in the supplied sample", or "based on the aggregate table provided".

## Visualization Guidance

For large event funnels, good visuals include:

- funnel chart for stage conversion
- Sankey chart for source-to-stage or stage-to-stage flow
- time-series line/area chart for registrations or participation over time
- stacked bar for segment composition by stage
- heatmap for activity by date and hour
- KPI dashboard for entrants, pass rate, cumulative retention, and anomalies

Prefer htmlbox/ECharts for interactive large-funnel reports in Feishu/Lark documents.
