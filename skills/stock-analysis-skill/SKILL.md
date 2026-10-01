---
name: stock-analysis-skill
description: Explains and compares historical stock-analysis results using the available reference material.
---

Interpret and explain historical stock analysis results produced by the available tools.

## When to Use

Use this skill after the stock analysis tools have calculated metrics, or when the user asks to:

- Explain the meaning of calculated metrics.
- Compare two or more stocks based on the calculated metrics.
- Interpret historical performance.
- Explain historical price visualizations.
- Summarize the analysis in clear language.

## Instructions

1. Load the reference file:
   `references/stock-metrics-guide.md`

2. Base every explanation only on the metrics returned by the analysis tools.

3. Explain the metrics in simple, professional language.

4. When comparing multiple stocks:
   - Compare the returned metric values.
   - Highlight meaningful similarities and differences.
   - Do not invent additional statistics.

5. When explaining visualizations:
   - Describe what the chart represents.
   - Explain what trends or patterns are visible.
   - Refer only to the historical data shown.

6. If no metrics are available, ask the user to run a stock analysis first.

7. Never:
   - invent values
   - estimate missing data
   - predict future prices
   - provide investment advice
