# TradingAgents -- State Management

## AgentState Structure

The core state object extends LangGraph's `MessagesState`:

```mermaid
stateDiagram-v2
    state AgentState {
        company_of_interest: Company ticker
        trade_date: Trading date
        --
        market_report: Market Analyst output
        sentiment_report: Social Analyst output
        news_report: News Analyst output
        fundamentals_report: Fundamentals Analyst output
        --
        investment_debate_state: InvestDebateState
        investment_plan: Research Manager output
        --
        trader_investment_plan: Trader output
        --
        risk_debate_state: RiskDebateState
        final_trade_decision: Portfolio Manager output
    }
```

## InvestDebateState

```mermaid
stateDiagram-v2
    state InvestDebateState {
        bull_history: Accumulated bullish arguments
        bear_history: Accumulated bearish arguments
        history: Full debate transcript
        current_response: Latest argument
        judge_decision: Research Manager verdict
        count: Current debate round
    }
```

## RiskDebateState

```mermaid
stateDiagram-v2
    state RiskDebateState {
        aggressive_history: Aggressive agent arguments
        conservative_history: Conservative agent arguments
        neutral_history: Neutral agent arguments
        history: Full debate transcript
        latest_speaker: Last speaker identity
        current_aggressive_response: Latest aggressive input
        current_conservative_response: Latest conservative input
        current_neutral_response: Latest neutral input
        judge_decision: Portfolio Manager verdict
        count: Current debate round
    }
```

## Pipeline State Machine

```mermaid
stateDiagram-v2
    [*] --> Initialized: propagate(company, date)
    
    state "Phase 1: Analysis" as P1 {
        [*] --> AnalystsRunning
        AnalystsRunning --> ToolCalling: LLM requests tool
        ToolCalling --> AnalystsRunning: Tool returns data
        AnalystsRunning --> ReportsClear: Messages cleared
        ReportsClear --> AnalystsComplete: All reports generated
    }
    
    Initialized --> P1
    
    state "Phase 2: Research Debate" as P2 {
        [*] --> BullArgument
        BullArgument --> BearArgument
        BearArgument --> JudgeEvaluation
        JudgeEvaluation --> BullArgument: Continue debate
        JudgeEvaluation --> DebateResolved: Decision reached
    }
    
    P1 --> P2
    
    state "Phase 3: Trading Plan" as P3 {
        [*] --> TraderAnalysis
        TraderAnalysis --> PlanGenerated
    }
    
    P2 --> P3
    
    state "Phase 4: Risk Assessment" as P4 {
        [*] --> RiskDebate
        RiskDebate --> AggressiveInput
        AggressiveInput --> NeutralInput
        NeutralInput --> ConservativeInput
        ConservativeInput --> PortfolioManagerReview
        PortfolioManagerReview --> RiskDebate: Continue discussion
        PortfolioManagerReview --> FinalDecision: Decision reached
    }
    
    P3 --> P4
    
    P4 --> SignalProcessed: Process final decision
    SignalProcessed --> Logged: Save state to JSON
    Logged --> [*]
```

## Debate Round Control

```mermaid
stateDiagram-v2
    [*] --> Round1
    
    state Round1 {
        [*] --> Participant1Speaks
        Participant1Speaks --> Participant2Speaks
        Participant2Speaks --> JudgeReview
    }
    
    JudgeReview --> |count < max_rounds| RoundN
    JudgeReview --> |count >= max_rounds| FinalDecision
    
    state RoundN {
        [*] --> Participant1SpeaksAgain
        Participant1SpeaksAgain --> Participant2SpeaksAgain
        Participant2SpeaksAgain --> JudgeReviewAgain
    }
    
    JudgeReviewAgain --> |continue| RoundN
    JudgeReviewAgain --> |done| FinalDecision
    
    FinalDecision --> [*]
```

## Memory System

```mermaid
stateDiagram-v2
    state "FinancialSituationMemory" as FSM {
        [*] --> Empty
        Empty --> Stored: store(situation, analysis)
        Stored --> Retrieved: retrieve(query)
        Retrieved --> Stored: New analysis added
    }
    
    state "Memory Instances" as MI {
        BM: bull_memory -- Past bullish analyses
        BEM: bear_memory -- Past bearish analyses
        TM: trader_memory -- Past trading plans
        IJM: invest_judge_memory -- Past judge decisions
        PMM: portfolio_manager_memory -- Past portfolio decisions
    }
```

Each memory instance is independent and role-specific. Memories persist across multiple `propagate()` calls, allowing agents to reference their own historical analyses.

## Configuration State

| Parameter | Default | Description |
|-----------|---------|-------------|
| `llm_provider` | `"openai"` | LLM provider (openai/anthropic/google) |
| `deep_think_llm` | `"gpt-5.4"` | Model for critical decisions |
| `quick_think_llm` | `"gpt-5.4-mini"` | Model for analysis |
| `max_debate_rounds` | `1` | Max rounds for bull/bear debate |
| `max_risk_discuss_rounds` | `1` | Max rounds for risk debate |
| `max_recur_limit` | `100` | LangGraph recursion limit |
| `output_language` | `"English"` | Output language for reports |
| `data_vendors.core_stock_apis` | `"yfinance"` | Stock data vendor |
| `data_vendors.technical_indicators` | `"yfinance"` | Indicators vendor |
| `data_vendors.fundamental_data` | `"yfinance"` | Fundamentals vendor |
| `data_vendors.news_data` | `"yfinance"` | News vendor |

## Signal Output

The `SignalProcessor` converts the portfolio manager's natural language decision into a standardized signal:

| Signal | Meaning |
|--------|---------|
| `BUY` | Strong buy recommendation |
| `SELL` | Strong sell recommendation |
| `HOLD` | Hold current position |

---
## See Also
- [README](README.md) — Project overview and quick start
- [Architecture](architecture.md) — System design and components
- [Workflow](workflow.md) — Event flows and processing pipelines
- [Development](development.md) — Development guide and best practices
