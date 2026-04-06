# TradingAgents -- Workflow

## Trading Decision Pipeline

```mermaid
sequenceDiagram
    participant U as User
    participant TAG as TradingAgentsGraph
    participant AN as Analysts (4)
    participant BR as Bull Researcher
    participant BER as Bear Researcher
    participant RM as Research Manager
    participant TR as Trader
    participant RD as Risk Debaters (3)
    participant PM as Portfolio Manager

    U->>TAG: propagate(company, date)
    TAG->>TAG: Create initial state

    par Parallel Analysis
        TAG->>AN: Market Analyst (tools: stock data, indicators)
        TAG->>AN: Social Analyst (tools: news)
        TAG->>AN: News Analyst (tools: news, insider)
        TAG->>AN: Fundamentals Analyst (tools: financials)
    end

    AN-->>TAG: market_report, sentiment_report, news_report, fundamentals_report

    par Research Debate
        TAG->>BR: Build bullish case (with bull_memory)
        TAG->>BER: Build bearish case (with bear_memory)
    end

    loop Max debate rounds
        BR-->>RM: Bullish argument
        BER-->>RM: Bearish argument
        RM->>RM: Evaluate arguments
        alt Debate continues
            RM->>BR: Continue debate
            RM->>BER: Continue debate
        else Decision reached
            RM-->>TAG: investment_plan (judge_decision)
        end
    end

    TAG->>TR: Create trading plan (with trader_memory)
    TR-->>TAG: trader_investment_plan

    par Risk Debate
        TAG->>RD: Aggressive Debator
        TAG->>RD: Neutral Debator
        TAG->>RD: Conservative Debator
    end

    loop Max risk discuss rounds
        RD-->>PM: Risk perspectives
        alt Debate continues
            PM->>RD: Continue risk discussion
        else Decision reached
            PM-->>TAG: final_trade_decision
        end
    end

    TAG->>TAG: Process signal
    TAG-->>U: (final_state, processed_signal)
```

## Analyst Tool Usage Flow

```mermaid
sequenceDiagram
    participant A as Analyst Agent
    participant TN as ToolNode
    participant DI as Data Interface
    participant DC as Data Cache
    participant API as External API

    A->>TN: Call tool (e.g., get_stock_data)
    TN->>DI: Request data
    DI->>DC: Check cache
    
    alt Cache hit
        DC-->>DI: Cached data
    else Cache miss
        DI->>API: Fetch from yfinance/Alpha Vantage
        API-->>DI: Raw data
        DI->>DC: Store in cache
    end
    
    DI-->>TN: Formatted data
    TN-->>A: Tool result
    A->>A: Generate analysis report
```

## Reflection Workflow

```mermaid
sequenceDiagram
    participant U as User
    participant TAG as TradingAgentsGraph
    participant RF as Reflector
    participant LLM as Quick Think LLM

    Note over TAG: After propagate() completes
    U->>TAG: reflect()
    TAG->>RF: Analyze current state
    RF->>LLM: Evaluate decision quality
    LLM-->>RF: Reflection analysis
    RF-->>TAG: Updated state with reflection
    TAG-->>U: Reflected state
```

## Data Flow

```mermaid
graph LR
    subgraph "Data Vendors"
        YF[yfinance]
        AV[Alpha Vantage]
    end

    subgraph "Data Interface"
        CSA[Core Stock APIs]
        TIA[Technical Indicators]
        FDA[Fundamental Data]
        NDA[News Data]
    end

    subgraph "Tool Functions"
        GSD[get_stock_data]
        GI[get_indicators]
        GF[get_fundamentals]
        GBS[get_balance_sheet]
        GCF[get_cashflow]
        GIS[get_income_statement]
        GN[get_news]
        GGN[get_global_news]
        GIT[get_insider_transactions]
    end

    subgraph "Cache"
        DC[data_cache/]
    end

    YF --> CSA
    AV --> CSA
    YF --> TIA
    AV --> TIA
    YF --> FDA
    AV --> FDA
    YF --> NDA
    AV --> NDA

    CSA --> GSD
    TIA --> GI
    FDA --> GF
    FDA --> GBS
    FDA --> GCF
    FDA --> GIS
    NDA --> GN
    NDA --> GGN
    NDA --> GIT

    GSD --> DC
    GI --> DC
    GF --> DC
```

## Agent State Propagation

```mermaid
graph TB
    subgraph "AgentState (LangGraph MessagesState)"
        CI[company_of_interest]
        TD[trade_date]
        MR[market_report]
        SR[sentiment_report]
        NR[news_report]
        FR[fundamentals_report]
        IDS[investment_debate_state]
        IP[investment_plan]
        TIP[trader_investment_plan]
        RDS[risk_debate_state]
        FTD[final_trade_decision]
    end

    A1[Analysts] --> MR
    A1 --> SR
    A1 --> NR
    A1 --> FR
    
    MR --> IDS
    SR --> IDS
    NR --> IDS
    FR --> IDS
    
    IDS --> IP
    IP --> TIP
    TIP --> RDS
    RDS --> FTD
```

## Multi-Day Trading Simulation

```mermaid
sequenceDiagram
    participant U as User
    participant TAG as TradingAgentsGraph
    participant LOG as State Logger

    loop For each trading day
        U->>TAG: propagate(company, date)
        TAG->>TAG: Run full pipeline
        TAG-->>U: (state, signal)
        TAG->>LOG: Log state to JSON
        
        opt Reflection
            U->>TAG: reflect()
            TAG-->>U: Reflection analysis
        end
        
        U->>U: Record decision (buy/sell/hold)
    end
    
    U->>LOG: Analyze historical decisions
```

---
## See Also
- [README](README.md) — Project overview and quick start
- [Architecture](architecture.md) — System design and components
- [State Management](state-management.md) — State lifecycle and data models
- [Development](development.md) — Development guide and best practices
