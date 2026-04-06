# TradingAgents -- Architecture

## System Design

TradingAgents uses LangGraph to orchestrate multiple specialized LLM agents through a directed state graph. The graph flows through four phases: analysis, research debate, trading plan, and risk evaluation.

## Trading Paradigm & Key Features

| Feature | Support | Details |
|---------|---------|---------|
| Backtesting Approach | Event-driven | Multi-day simulation via sequential `propagate()` calls; no vectorized backtesting |
| Live Trading | No | Generates BUY/SELL/HOLD signals but does not connect to brokers or execute orders |
| Paper Trading | No | No simulated execution engine |
| Multi-Asset | No | Single-stock analysis per `propagate()` call (one company at a time) |
| Data Feeds | yfinance + Alpha Vantage | Stock data, fundamentals, news, insider transactions; configurable per-tool vendor |
| ML Integration | Yes | LLM-powered agents (OpenAI, Anthropic, Google) for analysis, debate, and decision-making |
| Risk Management | Built-in | Three-perspective risk debate (aggressive, neutral, conservative) with portfolio manager final gate |
| Optimization | No | No hyperparameter or strategy optimization; debate rounds are configurable |
| Execution | None | Signal generation only (BUY/SELL/HOLD); no broker integration |

## High-Level Architecture

```mermaid
graph TB
    subgraph "Phase 1: Analysis"
        MA[Market Analyst]
        SA[Social Media Analyst]
        NA[News Analyst]
        FA[Fundamentals Analyst]
        MT[Market Tools]
        ST[Social Tools]
        NT[News Tools]
        FT[Fundamentals Tools]
    end

    subgraph "Phase 2: Research Debate"
        BR[Bull Researcher]
        BER[Bear Researcher]
        RM[Research Manager<br/>Judge]
    end

    subgraph "Phase 3: Trading"
        TR[Trader]
    end

    subgraph "Phase 4: Risk Management"
        AD[Aggressive Debator]
        ND[Neutral Debator]
        CD[Conservative Debator]
        PM[Portfolio Manager<br/>Final Decision]
    end

    subgraph "Memory"
        BM[Bull Memory]
        BEM[Bear Memory]
        TM[Trader Memory]
        IJM[Invest Judge Memory]
        PMM[Portfolio Manager Memory]
    end

    MA --> MT
    SA --> ST
    NA --> NT
    FA --> FT

    MA --> BR
    SA --> BR
    NA --> BR
    FA --> BR
    MA --> BER
    SA --> BER
    NA --> BER
    FA --> BER

    BR --> RM
    BER --> RM
    BM --> BR
    BEM --> BER
    IJM --> RM

    RM --> TR
    TM --> TR

    TR --> AD
    TR --> ND
    TR --> CD
    AD --> PM
    ND --> PM
    CD --> PM
    PMM --> PM
```

## Component Architecture

```mermaid
graph LR
    subgraph "Graph Engine"
        TAG[TradingAgentsGraph]
        GS[GraphSetup]
        CL[ConditionalLogic]
        PR[Propagator]
        RF[Reflector]
        SP[SignalProcessor]
    end

    subgraph "LLM Layer"
        LCF[LLM Client Factory]
        OAI[OpenAI Client]
        ANT[Anthropic Client]
        GOO[Google Client]
        DT[Deep Think LLM]
        QT[Quick Think LLM]
    end

    subgraph "Data Layer"
        DI[Data Interface]
        YF[yfinance]
        AV[Alpha Vantage]
        DC[Data Cache]
    end

    subgraph "Tool Nodes"
        TN1[Market Tools<br/>get_stock_data, get_indicators]
        TN2[Social Tools<br/>get_news]
        TN3[News Tools<br/>get_news, get_global_news,<br/>get_insider_transactions]
        TN4[Fundamentals Tools<br/>get_fundamentals, get_balance_sheet,<br/>get_cashflow, get_income_statement]
    end

    TAG --> GS
    TAG --> PR
    TAG --> RF
    TAG --> SP
    GS --> CL
    TAG --> LCF
    LCF --> OAI
    LCF --> ANT
    LCF --> GOO
    LCF --> DT
    LCF --> QT
    DI --> YF
    DI --> AV
    DI --> DC
    TN1 --> DI
    TN2 --> DI
    TN3 --> DI
    TN4 --> DI
```

## LangGraph State Graph

```mermaid
graph TB
    START((START))
    
    subgraph "Parallel Analysts"
        MA[Market Analyst]
        SA[Social Analyst]
        NA[News Analyst]
        FA[Fundamentals Analyst]
        MCA[Msg Clear Market]
        MCS[Msg Clear Social]
        MCN[Msg Clear News]
        MCF[Msg Clear Fundamentals]
        TM[tools_market]
        TS[tools_social]
        TN[tools_news]
        TF[tools_fundamentals]
    end

    BR[Bull Researcher]
    BER[Bear Researcher]
    RM[Research Manager]
    TRADER[Trader]
    
    subgraph "Risk Debate"
        AGG[Aggressive Analyst]
        NEU[Neutral Analyst]
        CON[Conservative Analyst]
    end
    
    PM[Portfolio Manager]
    END_NODE((END))

    START --> MA
    START --> SA
    START --> NA
    START --> FA
    
    MA --> TM
    TM --> |continue| MA
    TM --> |done| MCA
    SA --> TS
    TS --> |continue| SA
    TS --> |done| MCS
    NA --> TN
    TN --> |continue| NA
    TN --> |done| MCN
    FA --> TF
    TF --> |continue| FA
    TF --> |done| MCF
    
    MCA --> BR
    MCS --> BR
    MCN --> BR
    MCF --> BR
    MCA --> BER
    MCS --> BER
    MCN --> BER
    MCF --> BER
    
    BR --> RM
    BER --> RM
    RM --> |continue debate| BR
    RM --> |done| TRADER
    
    TRADER --> AGG
    TRADER --> NEU
    TRADER --> CON
    AGG --> PM
    NEU --> PM
    CON --> PM
    PM --> |continue debate| AGG
    PM --> END_NODE
```

## Key Design Patterns

### Dual LLM Strategy
The system uses two LLM tiers: a "deep thinking" model (e.g., GPT-4) for critical decisions (research judgment, portfolio management) and a "quick thinking" model (e.g., GPT-4-mini) for analysis and data processing.

### Tool-Augmented Agents
Each analyst type has a ToolNode containing data retrieval functions. The LLM agents call these tools to fetch real market data before generating their analysis.

### Debate Pattern
Both the research phase (bull vs. bear) and risk management phase (aggressive vs. conservative vs. neutral) use structured debates with configurable round limits. A judge agent synthesizes the debate into a final recommendation.

### Financial Situation Memory
Each key agent maintains a `FinancialSituationMemory` that stores past analyses and decisions. This provides context for current decisions and improves consistency.

### Configurable Data Vendors
The `src/tradingagents/dataflows/config.py` system allows switching between data vendors (yfinance, Alpha Vantage) at both category and individual tool levels, with caching to avoid redundant API calls.

## Agent Roles

| Agent | LLM Tier | Role |
|-------|----------|------|
| Market Analyst | Quick | Technical analysis with stock data and indicators |
| Social Media Analyst | Quick | Social sentiment analysis |
| News Analyst | Quick | Current affairs and insider trading analysis |
| Fundamentals Analyst | Quick | Balance sheet, cash flow, income analysis |
| Bull Researcher | Quick | Build bullish investment case |
| Bear Researcher | Quick | Build bearish investment case |
| Research Manager | Deep | Judge bull/bear debate, decide direction |
| Trader | Quick | Create actionable investment plan |
| Aggressive Debator | Quick | Risk-tolerant risk assessment |
| Neutral Debator | Quick | Balanced risk assessment |
| Conservative Debator | Quick | Risk-averse risk assessment |
| Portfolio Manager | Deep | Final trade decision incorporating risk |

---
## See Also
- [README](README.md) — Project overview and quick start
- [Workflow](workflow.md) — Event flows and processing pipelines
- [State Management](state-management.md) — State lifecycle and data models
- [Development](development.md) — Development guide and best practices
