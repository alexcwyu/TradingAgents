# TradingAgents -- Development Guide

## Setup

### Installation

```bash
pip install tradingagents
```

### From Source

```bash
git clone https://github.com/TradingAgents-AI/TradingAgents.git
cd TradingAgents
pip install -e .
```

### API Keys

Set environment variables for your LLM and data providers:

```bash
# LLM Providers (choose one or more)
export OPENAI_API_KEY="..."
export ANTHROPIC_API_KEY="..."
export GOOGLE_API_KEY="..."

# Data Providers (optional, yfinance is free)
export ALPHA_VANTAGE_API_KEY="..."
```

## Basic Usage

### Simple Trading Decision

```python
from tradingagents.graph.trading_graph import TradingAgentsGraph

# Create with default config (OpenAI + yfinance)
graph = TradingAgentsGraph()

# Get trading decision for a company on a date
final_state, signal = graph.propagate("AAPL", "2024-01-15")

print(f"Signal: {signal}")  # BUY, SELL, or HOLD
print(f"Decision: {final_state['final_trade_decision']}")
```

### Custom Configuration

```python
from tradingagents.graph.trading_graph import TradingAgentsGraph

config = {
    "llm_provider": "anthropic",
    "deep_think_llm": "claude-sonnet-4-20250514",
    "quick_think_llm": "claude-haiku-4-20250414",
    "max_debate_rounds": 2,
    "max_risk_discuss_rounds": 2,
    "data_vendors": {
        "core_stock_apis": "yfinance",
        "technical_indicators": "yfinance",
        "fundamental_data": "alpha_vantage",
        "news_data": "yfinance",
    },
    "output_language": "English",
}

graph = TradingAgentsGraph(config=config)
final_state, signal = graph.propagate("MSFT", "2024-03-01")
```

### Select Specific Analysts

```python
# Only use market and fundamentals analysts (skip social/news)
graph = TradingAgentsGraph(
    selected_analysts=["market", "fundamentals"],
    debug=True,  # Print agent outputs
)

final_state, signal = graph.propagate("TSLA", "2024-06-15")
```

### Multi-Day Simulation

```python
from datetime import date, timedelta

graph = TradingAgentsGraph()
results = []

start = date(2024, 1, 1)
for i in range(30):
    trade_date = start + timedelta(days=i)
    if trade_date.weekday() < 5:  # Skip weekends
        state, signal = graph.propagate("AAPL", str(trade_date))
        results.append({
            "date": str(trade_date),
            "signal": signal,
            "decision": state["final_trade_decision"],
        })

# Results include memory-informed decisions
```

### Reflection

```python
graph = TradingAgentsGraph()
state, signal = graph.propagate("NVDA", "2024-02-15")

# Reflect on the decision
reflection = graph.reflect()
print(reflection)
```

## Configuration Reference

### LLM Settings

| Parameter | Type | Options | Default |
|-----------|------|---------|---------|
| `llm_provider` | str | `"openai"`, `"anthropic"`, `"google"` | `"openai"` |
| `deep_think_llm` | str | Model name | `"gpt-5.4"` |
| `quick_think_llm` | str | Model name | `"gpt-5.4-mini"` |
| `backend_url` | str | API endpoint | `"https://api.openai.com/v1"` |
| `google_thinking_level` | str | `"high"`, `"minimal"` | `None` |
| `openai_reasoning_effort` | str | `"high"`, `"medium"`, `"low"` | `None` |
| `anthropic_effort` | str | `"high"`, `"medium"`, `"low"` | `None` |

### Debate Settings

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `max_debate_rounds` | int | 1 | Max rounds for bull/bear research debate |
| `max_risk_discuss_rounds` | int | 1 | Max rounds for risk management debate |
| `max_recur_limit` | int | 100 | LangGraph maximum recursion limit |

### Data Vendor Settings

| Parameter | Options | Default |
|-----------|---------|---------|
| `data_vendors.core_stock_apis` | `"yfinance"`, `"alpha_vantage"` | `"yfinance"` |
| `data_vendors.technical_indicators` | `"yfinance"`, `"alpha_vantage"` | `"yfinance"` |
| `data_vendors.fundamental_data` | `"yfinance"`, `"alpha_vantage"` | `"yfinance"` |
| `data_vendors.news_data` | `"yfinance"`, `"alpha_vantage"` | `"yfinance"` |

Per-tool overrides via `tool_vendors`:
```python
config["tool_vendors"] = {
    "get_stock_data": "alpha_vantage",  # Override category default
}
```

## Adding a New Analyst

1. Create agent file in `src/tradingagents/agents/analysts/`:

```python
# agents/analysts/custom_analyst.py
def create_custom_analyst(llm):
    def custom_analyst(state):
        # Access state data
        company = state["company_of_interest"]
        date = state["trade_date"]
        
        # Use LLM with tools to generate analysis
        response = llm.invoke(f"Analyze {company} as of {date}")
        
        return {"custom_report": response.content}
    return custom_analyst
```

2. Add tool node in `src/tradingagents/graph/trading_graph.py`
3. Register in `src/tradingagents/graph/setup.py`

## Adding a New Data Vendor

1. Implement data functions in `src/tradingagents/dataflows/`:

```python
# dataflows/custom_vendor.py
def get_stock_data_custom(ticker, date):
    # Fetch data from custom source
    return formatted_data
```

2. Register in `src/tradingagents/dataflows/interface.py`
3. Add to `data_vendors` config options

## Project Structure

```
tradingagents/
    agents/
        analysts/       # Market, Social, News, Fundamentals analysts
        researchers/    # Bull and Bear researchers
        managers/       # Research Manager, Portfolio Manager
        risk_mgmt/      # Aggressive, Conservative, Neutral debators
        trader/         # Trader agent
        utils/          # Agent states, memory, tools
    dataflows/          # Data vendor implementations and interface
    graph/              # LangGraph setup, propagation, reflection
    llm_clients/        # Multi-provider LLM client factory
```

## Important Notes

- Each `propagate()` call runs the full agent pipeline (can take 30-120s depending on LLM)
- Memory persists across calls -- agents learn from previous analyses
- Debug mode (`debug=True`) prints all agent outputs for inspection
- Data is cached in `src/tradingagents/dataflows/data_cache/` to avoid redundant API calls
- The system expects trading days only -- skip weekends/holidays
- Internal debate always uses English for reasoning quality; output language is configurable

## Troubleshooting

### `openai.AuthenticationError` or `anthropic.AuthenticationError`
The LLM API key is missing or invalid. Set the correct environment variable: `OPENAI_API_KEY` for OpenAI, `ANTHROPIC_API_KEY` for Anthropic, `GOOGLE_API_KEY` for Google. Verify the key is active and has sufficient quota.

### `propagate()` takes 60+ seconds
Each call runs the full agent pipeline (analysts, researchers, debaters, trader, portfolio manager). Reduce latency by selecting fewer analysts (`selected_analysts=["market"]`), lowering `max_debate_rounds` to 1, or using faster models like `gpt-4o-mini`.

### `RecursionError` or `max_recur_limit` exceeded
LangGraph hit the recursion limit during graph execution. Increase `max_recur_limit` in the config (default: 100). This usually indicates too many debate rounds -- reduce `max_debate_rounds` and `max_risk_discuss_rounds`.

### `yfinance` returns empty data or `None`
yfinance relies on Yahoo Finance which can be unreliable. Verify the ticker symbol exists and the date is a valid trading day (not a weekend or holiday). Data is cached in `dataflows/data_cache/` -- delete the cache directory to force a fresh fetch.

### `KeyError: 'final_trade_decision'` in returned state
The agent pipeline did not complete successfully. Enable `debug=True` to see which agent failed. This commonly happens when the LLM returns an unexpected format. Check that your model supports tool calling (required for analyst agents).

### `alpha_vantage` rate limiting (5 calls/minute on free tier)
Alpha Vantage's free tier allows only 5 API calls per minute. The framework makes multiple data calls per propagation. Either use a premium key, switch `data_vendors` to `"yfinance"` (no rate limit), or add delays between `propagate()` calls.

### Memory grows across multiple `propagate()` calls
`FinancialSituationMemory` accumulates context across calls (by design). For long simulations, this increases prompt length and cost. There is no built-in memory pruning -- consider creating a fresh `TradingAgentsGraph` instance periodically.

### Google Gemini models return empty or malformed responses
Set `google_thinking_level` to `"high"` in the config for complex analysis tasks. The `"minimal"` level may produce truncated responses for multi-step reasoning.

## Security Considerations

- **API key management**: LLM provider keys (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `GOOGLE_API_KEY`) and data provider keys (`ALPHA_VANTAGE_API_KEY`) should be set via environment variables, not in code or config files. These keys provide billable API access.
- **Cost controls**: Each `propagate()` call makes 10-15 LLM API calls. Set up spending limits with your LLM provider. Monitor token usage via LangGraph callbacks (pass `callbacks` parameter to `TradingAgentsGraph`). Use `quick_think_llm` (cheaper model) for non-critical agents.
- **Data cache security**: Market data is cached locally in `dataflows/data_cache/`. This directory may contain financial data subject to redistribution restrictions. Do not share or commit cache files to public repositories.
- **Prompt injection risk**: Financial news and social media data are fed directly into LLM prompts. Adversarial content in news articles or social posts could influence agent reasoning. Do not use agent signals for automated trading without human review.
- **No trade execution**: TradingAgents produces BUY/SELL/HOLD signals but does not execute trades. The framework has no broker integration. Treat signals as research input, not trading instructions.
- **Memory persistence**: `FinancialSituationMemory` stores analysis context on disk. This may contain proprietary analysis or sensitive financial opinions. Secure the `results_dir` directory and clean up after simulation runs.
- **Backend URL configuration**: The `backend_url` config parameter allows routing LLM calls to custom endpoints (e.g., Azure OpenAI, local models). Verify the endpoint uses HTTPS and belongs to a trusted provider. Do not route through untrusted proxies.

---
## See Also
- [README](README.md) — Project overview and quick start
- [Architecture](architecture.md) — System design and components
- [Workflow](workflow.md) — Event flows and processing pipelines
- [State Management](state-management.md) — State lifecycle and data models
