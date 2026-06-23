# EDA Output Formats

Use this reference when deciding how to deliver the final EDA result.

## Decision Rule

Default to `markdown` unless the user requests a specific target. Prefer the richest requested target that can be safely produced with available tools.

| user wording | choose |
|---|---|
| "report", "markdown", "README", "send me the analysis" | `markdown` |
| "Feishu", "Lark", "飞书文档", "云文档", "docx" | `feishu-doc` |
| "interactive", "dynamic", "dashboard", "ECharts", "htmlbox", "动图", "可交互图表", "数据大屏" | `feishu-doc-htmlbox` |
| "reproducible", "handoff", "give me files", "analysis artifacts" | `artifacts` |

If the user asks for Feishu output and also asks for visualizations, choose `feishu-doc-htmlbox`.

## Option: markdown

Deliver:
- one Markdown report
- Mermaid diagrams when they clarify structure
- exact tables for numbers
- optional local image files if matplotlib or another renderer is useful

Use this for quick reviews, GitHub issues, repository docs, and plain-text handoff.

## Option: feishu-doc

Deliver:
- a Feishu/Lark document URL
- structured document sections
- callouts for headline findings and caveats
- tables for exact values
- Mermaid or whiteboard blocks when static visuals are enough

Use this for business-facing, shareable reports that do not need interactive charts.

Implementation notes:
- Use `lark-cli docs +create --api-version v2 --as user` for a new document.
- Use XML document content when building structured docs.
- Keep the opening callout short and decision-oriented.
- Do not embed private raw rows unless the user explicitly asks.

## Option: feishu-doc-htmlbox

Deliver:
- a Feishu/Lark document URL
- narrative sections and exact summary tables
- one or more htmlbox blocks for interactive or animated charts
- a short appendix listing assumptions, chart sources, and limitations

Use this for EDA outputs where visualization is central.

Recommended chart mapping:

| EDA need | htmlbox chart |
|---|---|
| time trend | ECharts line or gradient area |
| top categories | bar chart |
| category composition | stacked bar, pie, treemap, or sunburst |
| missingness matrix | heatmap |
| date coverage | calendar heatmap |
| numeric distribution | boxplot, histogram, or scatter/bubble |
| group differences | grouped bar, boxplot, or radar |
| funnel / conversion | funnel |
| source-to-target flow | Sankey |
| KPI overview | CSS/JS KPI dashboard |
| relationship graph | force graph |

Required workflow:
1. Compute chart-ready aggregate data with Python.
2. Create a self-contained HTML file per chart using the `feishu-cli-htmlbox` recipes.
3. Verify each file locally with the htmlbox verification script before insertion.
4. Visually inspect the generated screenshot.
5. Insert verified charts with `feishu-cli doc htmlbox create`.
6. Keep chart titles in the Feishu document, not only inside the iframe.
7. Return the document URL and mention any generated local chart files.

Avoid:
- unverified HTML blocks
- raw PII embedded in JavaScript
- axes without units or denominators
- animated charts that distract from the finding
- too many charts without a narrative section between them

## Option: artifacts

Deliver a small folder or file set:
- Markdown report
- cleaned or normalized derived data if allowed
- aggregate CSV/JSON summaries
- chart HTML files
- generated static images
- notes on how to rerun the analysis

Use this when another analyst or agent will continue the work.
