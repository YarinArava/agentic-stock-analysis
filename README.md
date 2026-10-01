# Agentic Stock Analysis with Google ADK

An agentic stock-analysis assistant built with **Google ADK** that interprets natural-language requests, selects deterministic Python tools, analyzes historical stock data, generates visualizations, and maintains conversational context for follow-up requests.

The LLM is used for **reasoning and orchestration**, while financial calculations are performed by deterministic Python functions.

> **Disclaimer:** This project is for educational purposes only.  
> It analyzes historical market data and does not provide investment advice, trading recommendations, or future price predictions.

---

## Project Overview

The project demonstrates how an LLM-based agent can coordinate several specialized tools to complete a multi-step stock-analysis workflow.

A request such as:

```text
Compare Apple and Microsoft during the last year.
```

can trigger the following sequence:

1. Identify the requested companies
2. Resolve company names into ticker symbols
3. Download historical stock prices
4. Cache the retrieved data
5. Calculate financial metrics
6. Compare and explain the results
7. Generate visualizations when requested

The agent also maintains conversational context, allowing follow-up requests such as:

```text
Show the normalized price performance of them.
```

---

## Architecture

```text
Natural-Language Request
          │
          ▼
   Google ADK Agent
          │
          ├── Company Identification
          ├── Ticker Resolution
          ├── Historical Price Retrieval
          ├── Financial Metrics
          ├── Visualizations
          └── Stock Analysis Skill
                    │
                    ▼
             Final Response
```

The agent acts as the **reasoning and orchestration layer**, while deterministic tools handle data retrieval, numerical calculations, and visualization.

---

## Deterministic Tools

### Company Identification

Extracts supported company names or ticker symbols from natural-language requests.

Examples:

```text
Compare Apple and Microsoft
```

```text
Compare AAPL and MSFT
```

---

### Ticker Resolution

Maps supported company names into ticker symbols.

Examples:

```text
Apple → AAPL
Microsoft → MSFT
NVIDIA → NVDA
Google → GOOGL
```

Unsupported companies are handled gracefully instead of being guessed.

---

### Historical Price Retrieval

Historical stock data is retrieved using **yfinance**.

Supported periods include:

```text
1d
5d
1mo
3mo
6mo
1y
2y
5y
10y
max
```

Retrieved market data is stored in a local Python cache so that subsequent tools can reuse it without downloading the same data again.

---

## Financial Metrics

The project calculates several historical performance and risk metrics.

### Price Metrics

- **Latest Price** – Most recent closing price
- **Minimum Price** – Lowest closing price during the selected period
- **Maximum Price** – Highest closing price during the selected period
- **Average Price** – Mean closing price during the selected period

### Performance Metrics

- **Return (%)** – Percentage change between the first and last closing price

### Risk Metrics

- **Daily Volatility (%)** – Standard deviation of daily returns
- **Highest Daily Gain (%)** – Largest positive daily return
- **Largest Daily Loss (%)** – Largest negative daily return
- **Maximum Drawdown (%)** – Largest decline from a previous peak

These definitions are stored in the reusable reference file:

```text
skills/stock-analysis-skill/references/stock-metrics-guide.md
```

The agent is instructed to use only values returned by the analysis tools. :contentReference[oaicite:0]{index=0}

---

## Visualizations

The system supports three visualization types.

### Historical Closing Prices

Shows the actual historical closing-price movement over the selected period.

### Normalized Performance

All stocks are normalized to a starting value of:

```text
100
```

This enables relative performance comparison between stocks with different price scales.

### Drawdown

Displays the percentage decline from each stock's previous peak.

---

## Data Caching

Historical stock data is stored in:

```python
PRICE_CACHE
```

This allows the metrics and visualization tools to reuse the same downloaded data without repeatedly passing large datasets through the LLM.

---

## Conversational Memory

The project uses Google ADK's:

- `InMemorySessionService`
- `Runner`
- Session IDs

to preserve conversation state during an active session.

Example:

```text
User:
Compare Apple and Microsoft during the last year.

User:
Show the normalized performance of them.
```

The agent can interpret `"them"` using the existing session context.

---

## Reusable ADK Skill

The project includes a reusable Google ADK skill:

```text
stock-analysis-skill
```

The skill defines when and how the agent should explain stock-analysis results.

It is used for tasks such as:

- Explaining calculated financial metrics
- Comparing multiple stocks
- Interpreting historical performance
- Explaining generated visualizations
- Summarizing analysis results clearly

The skill explicitly instructs the agent to base explanations only on values returned by the tools and not invent additional statistics. :contentReference[oaicite:1]{index=1}

It also prohibits:

- Inventing values
- Estimating missing data
- Predicting future stock prices
- Providing investment advice :contentReference[oaicite:2]{index=2}

### Skill Structure

```text
skills/
└── stock-analysis-skill/
    ├── SKILL.md
    └── references/
        └── stock-metrics-guide.md
```

This separates reusable domain knowledge and interpretation rules from the main agent implementation.

---

## Guardrails

The agent is restricted to historical stock analysis.

The system is designed to:

- Reject unrelated requests
- Reject unsafe or malicious requests
- Avoid investment recommendations
- Avoid future price predictions
- Avoid inventing missing values
- Report tool failures instead of guessing

Model-level safety settings are also applied through Google ADK generation configuration.

---

## Error Handling

The tools return structured success or failure responses.

The system handles cases such as:

- Unsupported companies
- Invalid historical periods
- Missing ticker symbols
- Missing cached data
- Failed market-data downloads
- Insufficient historical observations

The agent explains the issue instead of generating unsupported results.

---

## Agent Components

### `LlmAgent`

Defines the agent's behavior, model, instructions, tools, skills, and generation configuration.

### `FunctionTool`

Exposes deterministic Python functions as tools available to the agent.

### `Runner`

Executes the agent and manages tool-call sequences.

### `InMemorySessionService`

Stores conversational state during the active session.

### `SkillToolset`

Provides the agent with reusable skill instructions and reference files.

---

## Model

The agent uses:

```text
Gemini 3.1 Flash Lite
```

through Google ADK and LiteLLM.

The LLM is responsible for:

- Understanding user requests
- Selecting the required tools
- Coordinating multi-step execution
- Explaining deterministic outputs in natural language

The LLM does **not** perform the financial calculations itself.

---

## Technologies

- Python
- Google Agent Development Kit (ADK)
- Gemini
- LiteLLM
- yfinance
- Pandas
- NumPy
- Matplotlib
- Python Dotenv
- Async Python

---

## Example Requests

```text
Compare Apple and Microsoft during the last year.
```

```text
Analyze NVIDIA during the last 6 months.
```

```text
Show the normalized price performance of them.
```

```text
Compare AAPL and Microsoft during the last year and show their drawdown charts.
```

---

## Project Structure

```text
agentic-stock-analysis/
│
├── README.md
├── agentic_stock_analysis.ipynb
├── requirements.txt
├── .gitignore
│
└── skills/
    └── stock-analysis-skill/
        ├── SKILL.md
        └── references/
            └── stock-metrics-guide.md
```

---

## Setup

Install the required dependencies:

```bash
pip install numpy pandas matplotlib yfinance google-adk litellm nest_asyncio python-dotenv
```

Create a `.env` file:

```text
GEMINI_API_KEY=YOUR_API_KEY
```

Add it to `.gitignore`:

```text
.env
__pycache__/
.ipynb_checkpoints/
```

---

## Key Concepts Demonstrated

- Agentic AI workflows
- LLM tool calling
- Multi-step task orchestration
- Deterministic computation with LLM coordination
- Conversational session state
- Data caching between tools
- Reusable ADK skills
- Structured error handling
- Guardrails and domain restrictions
- Financial-data visualization
- Natural-language interfaces for analytical tools

---

## Key Takeaway

The central idea of the project is to use the LLM as an **orchestrator rather than a calculator**.

The agent interprets natural-language requests and decides which actions are required, while deterministic Python tools handle market-data retrieval, numerical calculations, and visualization.

This combines the flexibility of an LLM with the reliability of conventional software tools.
