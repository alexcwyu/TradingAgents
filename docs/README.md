# TradingAgents

> **Last Updated**: 2026-04-06T16:25:30Z  \
> **Git Hash**: `854f1d2`

**Multi-agent LLM-powered financial trading framework using LangGraph for collaborative analysis and decision-making.**

| Field | Details |
|-------|---------|
| Language | Python |
| License | MIT |
| Author | TradingAgents Team |
| GitHub | [TradingAgents](https://github.com/TradingAgents-AI/TradingAgents) |
| LLM Framework | LangGraph |

## Overview

TradingAgents is a multi-agent framework that uses Large Language Models to make trading decisions through collaborative debate and analysis. It employs specialized analyst agents (market, social, news, fundamentals), researcher agents (bull/bear), risk management debaters (aggressive/conservative/neutral), a trader, and a portfolio manager -- all orchestrated through a LangGraph state graph. The system supports multiple LLM providers (OpenAI, Anthropic, Google) and data vendors (yfinance, Alpha Vantage).

## Key Features

### Trading Capabilities
- Multi-agent collaborative analysis with specialized roles
- Bull vs. bear researcher debate with judge evaluation
- Risk management with aggressive/conservative/neutral debaters
- Portfolio manager for final trade decisions
- Configurable analyst selection (market, social, news, fundamentals)
- Financial situation memory for context-aware decisions

### Architecture
- LangGraph-based state graph for agent orchestration
- Modular agent design with pluggable analysts
- Tool nodes for data retrieval (stock data, indicators, fundamentals, news)
- Signal processing for standardized trade outputs
- Reflection capability for post-decision analysis
- Dual LLM strategy: deep-thinking for critical decisions, quick-thinking for analysis

### Data Sources
- **Stock Data**: yfinance, Alpha Vantage (OHLCV, market snapshots)
- **Technical Indicators**: Moving averages, RSI, MACD, Bollinger Bands
- **Fundamentals**: Balance sheets, cash flow, income statements
- **News**: Financial news, insider transactions, global news
- **Social**: Social media sentiment analysis

### LLM Providers
- **OpenAI**: GPT-4, GPT-4-mini (default)
- **Anthropic**: Claude models
- **Google**: Gemini models with thinking levels

## Architecture Summary

```
Analysts (Market, Social, News, Fundamentals)
    |
    v
Bull Researcher <-> Bear Researcher (Debate)
    |
    v
Research Manager (Judge)
    |
    v
Trader (Investment Plan)
    |
    v
Risk Debaters (Aggressive, Conservative, Neutral)
    |
    v
Portfolio Manager (Final Decision)
```

## Component Table

| Component | Location | Purpose |
|-----------|----------|---------|
| TradingAgentsGraph | `src/tradingagents/graph/trading_graph.py` | Main orchestrator class |
| GraphSetup | `src/tradingagents/graph/setup.py` | LangGraph state graph construction |
| Propagator | `src/tradingagents/graph/propagation.py` | State initialization and graph execution |
| Reflector | `src/tradingagents/graph/reflection.py` | Post-decision reflection |
| SignalProcessor | `src/tradingagents/graph/signal_processing.py` | Standardize trade signals |
| ConditionalLogic | `src/tradingagents/graph/conditional_logic.py` | Graph routing conditions |
| MarketAnalyst | `src/tradingagents/agents/analysts/market_analyst.py` | Technical market analysis |
| SocialMediaAnalyst | `src/tradingagents/agents/analysts/social_media_analyst.py` | Social sentiment analysis |
| NewsAnalyst | `src/tradingagents/agents/analysts/news_analyst.py` | News analysis |
| FundamentalsAnalyst | `src/tradingagents/agents/analysts/fundamentals_analyst.py` | Fundamental analysis |
| BullResearcher | `src/tradingagents/agents/researchers/bull_researcher.py` | Bullish case builder |
| BearResearcher | `src/tradingagents/agents/researchers/bear_researcher.py` | Bearish case builder |
| ResearchManager | `src/tradingagents/agents/managers/research_manager.py` | Debate judge |
| PortfolioManager | `src/tradingagents/agents/managers/portfolio_manager.py` | Final decision maker |
| Trader | `src/tradingagents/agents/trader/trader.py` | Investment plan creator |
| AggressiveDebator | `src/tradingagents/agents/risk_mgmt/aggressive_debator.py` | Risk-tolerant perspective |
| ConservativeDebator | `src/tradingagents/agents/risk_mgmt/conservative_debator.py` | Risk-averse perspective |
| NeutralDebator | `src/tradingagents/agents/risk_mgmt/neutral_debator.py` | Balanced perspective |
| FinancialSituationMemory | `src/tradingagents/agents/utils/memory.py` | Persistent memory store |
| DataInterface | `src/tradingagents/dataflows/interface.py` | Unified data access |
| LLMClientFactory | `src/tradingagents/llm_clients/factory.py` | Multi-provider LLM creation |

## Quick Start

This example requires an OpenAI API key (or Anthropic/Google). Data comes from yfinance (free, no key needed).

```python
import os
os.environ["OPENAI_API_KEY"] = "sk-..."  # set your key

from tradingagents.graph.trading_graph import TradingAgentsGraph

# 1. Create graph with default config (OpenAI + yfinance)
graph = TradingAgentsGraph(
    selected_analysts=["market", "fundamentals"],  # skip social/news for speed
    debug=True,  # print agent outputs
)

# 2. Get a trading decision
final_state, signal = graph.propagate("AAPL", "2024-06-15")

print(f"Signal: {signal}")  # BUY, SELL, or HOLD
print(f"Decision: {final_state['final_trade_decision']}")

# 3. Reflect on the reasoning
reflection = graph.reflect()
print(f"Reflection: {reflection}")
```

### Cost Estimation

Each `propagate()` call runs multiple LLM invocations. Token usage varies by analyst count and debate rounds.

| Component | Calls per propagate | Est. Input Tokens | Est. Output Tokens |
|-----------|--------------------:|------------------:|-------------------:|
| Market Analyst | 1 | ~2,000 | ~500 |
| Fundamentals Analyst | 1 | ~3,000 | ~800 |
| News Analyst | 1 | ~2,500 | ~600 |
| Social Media Analyst | 1 | ~2,000 | ~500 |
| Bull Researcher | 1 per debate round | ~3,000 | ~1,000 |
| Bear Researcher | 1 per debate round | ~3,000 | ~1,000 |
| Research Manager (Judge) | 1 | ~4,000 | ~800 |
| Trader | 1 | ~3,000 | ~600 |
| Risk Debaters (x3) | 1 each per round | ~2,500 each | ~600 each |
| Portfolio Manager | 1 | ~4,000 | ~800 |
| **Total (4 analysts, 1 debate round)** | **~13** | **~35,000** | **~8,500** |
| **Total (2 analysts, 1 debate round)** | **~11** | **~28,000** | **~7,000** |

Approximate cost per call with GPT-4o: $0.15-$0.30. With GPT-4o-mini: $0.01-$0.03. Costs scale linearly with `max_debate_rounds` and `max_risk_discuss_rounds`.

## Links

- [Architecture](architecture.md) -- System design and component diagrams
- [Workflow](workflow.md) -- Agent flows and decision pipelines
- [State Management](state-management.md) -- State machines and agent states
- [Development](development.md) -- Setup, configuration, and customization
