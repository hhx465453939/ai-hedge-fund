---
agent_id: "risk_manager_agent"
agent_name: "Risk Manager"
version: "1.0.0"
agent_type: "manager"
enabled: true
priority: 9
tags:
  - "risk"
  - "portfolio"
  - "position-sizing"
  - "diversification"
created: "2024-10-18"
updated: "2024-10-18"
---

## Overview

The Risk Manager Agent is responsible for evaluating portfolio-level risk and setting position size limits for each stock recommendation. This agent operates after analyst and investor agents have generated their signals, aggregating their inputs to assess conviction levels and calculate appropriate position sizes based on risk metrics, diversification requirements, and predefined risk parameters.

## Strategy

### Investment Philosophy

Effective risk management is the cornerstone of long-term investment success. Even the best stock picks can lead to poor portfolio outcomes if position sizes are not properly calibrated. This agent ensures that the portfolio remains balanced, diversified, and aligned with risk tolerance, preventing catastrophic losses while allowing for meaningful upside participation.

**Core Principle**: "Risk comes from not knowing what you're doing." - Warren Buffett

The Risk Manager ensures systematic risk assessment so the portfolio has controlled exposure to any single stock, sector, or risk factor.

### Key Principles

1. **Diversification**: Spread risk across multiple stocks, sectors, and investment styles

2. **Position Sizing**: Allocate more capital to high-conviction ideas with lower risk

3. **Loss Prevention**: Limit maximum loss per position and portfolio drawdown

4. **Volatility Awareness**: Adjust position sizes based on individual stock volatility

5. **Correlation Management**: Avoid concentrated exposures to correlated assets

6. **Dynamic Adjustment**: Rebalance as market conditions and signals change

7. **Downside Focus**: Prioritize avoiding large losses over capturing all gains

### Decision Criteria

- **Primary Factors**: 
  - Signal consensus across agents (higher agreement = higher confidence)
  - Historical volatility (lower volatility = larger position)
  - Analyst confidence levels
  - Sector exposure limits

- **Secondary Factors**:
  - Correlation with existing positions
  - Liquidity and market cap
  - Recent price momentum
  - Maximum drawdown history

- **Risk Constraints**:
  - Max single position: 10% of portfolio
  - Max sector exposure: 25% of portfolio
  - Minimum positions: 10 stocks (for diversification)
  - Maximum positions: 30 stocks (for focus)
  - High-volatility stocks: Half position size

## Analysis Criteria

### Metrics

#### Signal Consensus Analysis (Weight: 40%)

Evaluate agreement across all agents:

| Consensus Level | Description | Conviction Score |
|----------------|-------------|------------------|
| Unanimous (100%) | All agents agree | 100 |
| Strong (75-99%) | 3/4 or more agents agree | 85 |
| Moderate (60-74%) | Clear majority agree | 70 |
| Weak (50-59%) | Slight majority agree | 55 |
| Divided (<50%) | No clear consensus | 25 |

**Calculation:**
```
Consensus = (Agents with same signal / Total agents) × 100
```

**Signal Strength Multiplier:**
- Unanimous bullish with high confidence: 1.5x base position
- Strong consensus: 1.25x base position
- Moderate consensus: 1.0x base position
- Weak consensus: 0.75x base position
- Divided: 0.5x base position or skip

#### Volatility Analysis (Weight: 30%)

Adjust position sizes based on historical volatility:

| Volatility Level | Annualized StdDev | Risk Factor | Position Adjustment |
|-----------------|------------------|-------------|---------------------|
| Very Low | < 15% | 1.3 | +30% position size |
| Low | 15-25% | 1.1 | +10% position size |
| Moderate | 25-35% | 1.0 | Base position size |
| High | 35-50% | 0.7 | -30% position size |
| Very High | > 50% | 0.5 | -50% position size |

**Calculation:**
```
Annualized Volatility = Daily StdDev × √252
Risk Adjusted Position = Base Position × Risk Factor
```

#### Sector Concentration (Weight: 15%)

Ensure portfolio diversification across sectors:

| Current Sector Exposure | Action | Position Adjustment |
|------------------------|--------|---------------------|
| < 15% | Free to add | 1.0x |
| 15-20% | Monitor | 0.9x |
| 20-25% | Caution | 0.7x |
| > 25% | Limit reached | 0.5x or skip |

**Special Rules:**
- Max 3 stocks per sector (for sector-concentrated portfolios)
- Tech/Growth sectors: 30% max combined
- Cyclical sectors: 20% max combined

#### Confidence-Weighted Score (Weight: 15%)

Aggregate confidence levels from all agents:

| Avg Confidence | Position Modifier |
|----------------|------------------|
| 90-100% | 1.3x |
| 80-89% | 1.15x |
| 70-79% | 1.0x |
| 60-69% | 0.85x |
| 50-59% | 0.7x |
| < 50% | 0.5x or skip |

**Calculation:**
```
Weighted Confidence = Σ(Agent Confidence × Agent Priority) / Σ(Agent Priority)
```

### Position Sizing Formula

```python
# Base position size
base_position = 100% / target_num_positions  # e.g., 100% / 20 = 5%

# Calculate final position
final_position = (
    base_position 
    × consensus_multiplier 
    × risk_factor 
    × confidence_modifier
    × sector_adjustment
)

# Apply hard limits
final_position = min(final_position, max_position_limit)  # Cap at 10%
final_position = max(final_position, min_position_limit)  # Floor at 1%
```

### Risk Limits

**Portfolio-Level Constraints:**

| Constraint | Limit | Rationale |
|-----------|-------|-----------|
| Max Single Position | 10% | Prevent concentration risk |
| Min Single Position | 1% | Avoid meaningless positions |
| Max Sector Exposure | 25% | Sector diversification |
| Max Correlation Cluster | 30% | Avoid correlated drawdowns |
| Target Number of Positions | 15-25 | Balance focus and diversification |
| Cash Reserve | 5-10% | Dry powder for opportunities |

**Position-Level Constraints:**

| Risk Metric | Calculation | Limit |
|-------------|-------------|-------|
| Stop Loss | Entry price × (1 - stop_loss_pct) | -15% max loss |
| Position Beta | β relative to market | < 2.0 for single position |
| Liquidity | Avg daily volume | Position < 5% of 30-day volume |
| Draw down Limit | Peak to trough | -25% triggers review |

## Implementation

### Data Requirements

- **Agent Signals**: 
  - All analyst signals (fundamentals, technicals, sentiment, valuation)
  - All investor signals (Buffett, Lynch, Munger, etc.)
  - Each signal includes: signal (bullish/bearish/neutral), confidence (0-100), reasoning
  
- **Price Data**:
  - Historical prices (1-2 years) for volatility calculation
  - Current price for position sizing
  - Average daily volume for liquidity check

- **Portfolio Data**:
  - Current positions and weights
  - Sector allocations
  - Available cash
  - Total portfolio value

- **Market Data**:
  - Sector classifications
  - Market cap
  - Beta (if available)

### Processing Steps

1. **Collect Agent Signals**:
   - Aggregate signals from all active agents
   - Extract signal direction, confidence, and reasoning
   - Filter out disabled agents

2. **Calculate Consensus**:
   - Count bullish, bearish, neutral signals
   - Calculate consensus percentage
   - Determine overall portfolio signal direction
   - Weight by agent priority (investor agents may have higher priority)

3. **Assess Risk Metrics**:
   - Calculate historical volatility (30-90 day rolling)
   - Determine risk factor based on volatility tier
   - Check sector concentration
   - Calculate average confidence across agents

4. **Compute Base Position Size**:
   - Determine target number of positions (e.g., 20)
   - Calculate base position: 100% / 20 = 5%

5. **Apply Multipliers**:
   - Consensus multiplier (unanimous = 1.5x)
   - Risk factor (volatility-based)
   - Confidence modifier
   - Sector adjustment

6. **Enforce Constraints**:
   - Apply maximum position limit (10%)
   - Apply minimum position limit (1%)
   - Check sector limits
   - Ensure total doesn't exceed 100%

7. **Set Stop Losses**:
   - Calculate stop loss levels (-15% default)
   - Adjust based on volatility (tighter stops for volatile stocks)

8. **Generate Output**:
   - Final position sizes for each ticker
   - Risk metrics summary
   - Rationale for sizing decisions
   - Recommended actions (buy, hold, sell, skip)

### Dependencies

- **API Requirements**: 
  - Historical price data for volatility calculation
  - Current portfolio positions
  - Sector classification data
  
- **External Services**: None

- **Other Agents**: 
  - All analyst agents (fundamentals, technicals, sentiment, valuation)
  - All investor agents (Buffett, Lynch, Munger, etc.)
  - Portfolio Manager (receives risk limits)

### Rate Limits

- **API Calls per Ticker**: 1-2 calls (price history, sector data)
- **Total API Calls**: (1-2) × number of tickers
- **Execution Time Estimate**: 1-2 seconds per ticker

### Error Handling

- **Missing Agent Signals**: Use available signals, adjust consensus calculation
- **Missing Price Data**: Use default moderate volatility assumption, reduce confidence
- **Sector Data Unavailable**: Classify as "Other", apply conservative limits
- **Constraint Violations**: Scale down all positions proportionally to fit constraints

## Examples

### Example 1: High Conviction, Low Risk (AAPL)

**Input:**

*Agent Signals:*
- Fundamentals Analyst: Bullish (confidence: 85%)
- Technicals Analyst: Bullish (confidence: 78%)
- Sentiment Analyst: Bullish (confidence: 72%)
- Valuation Analyst: Neutral (confidence: 65%)
- Warren Buffett Agent: Bullish (confidence: 88%)
- Peter Lynch Agent: Bullish (confidence: 82%)
- Charlie Munger Agent: Bullish (confidence: 80%)

*Risk Metrics:*
- 30-day volatility: 22% (Low)
- Current sector exposure (Tech): 18%
- Correlation with portfolio: 0.45 (moderate)

**Analysis:**

*Consensus:*
- Bullish signals: 6/7 = 86% (Strong consensus)
- Average confidence: 79%
- Consensus multiplier: 1.25x

*Risk Assessment:*
- Volatility: 22% → Risk factor 1.1x (Low volatility)
- Sector: 18% → Adjustment 1.0x (Below 20% limit)
- Confidence: 79% → Modifier 1.0x

*Position Calculation:*
```
Base position = 5% (assuming 20 target positions)
Final position = 5% × 1.25 × 1.1 × 1.0 × 1.0 = 6.875%
Capped at 10%: 6.875% (within limit)
```

*Stop Loss:*
- Entry: $175
- Stop: $175 × 0.85 = $148.75 (-15%)

**Output:**
```json
{
  "AAPL": {
    "position_size": 6.875,
    "position_sizing_reasoning": "Strong consensus (86%) across 6/7 bullish agents with average confidence of 79%. Low volatility (22%) allows for slightly larger position. Tech sector exposure at 18% is within limits. Final position: 6.9% of portfolio.",
    "risk_metrics": {
      "consensus": 86,
      "avg_confidence": 79,
      "volatility": 22,
      "risk_factor": 1.1,
      "sector_exposure": 18,
      "stop_loss": 148.75,
      "max_loss_pct": -15
    },
    "recommendation": "buy",
    "rationale": "High conviction opportunity with manageable risk profile"
  }
}
```

### Example 2: Moderate Conviction, High Volatility (NVDA)

**Input:**

*Agent Signals:*
- Fundamentals Analyst: Bullish (confidence: 75%)
- Technicals Analyst: Bullish (confidence: 82%)
- Sentiment Analyst: Neutral (confidence: 60%)
- Valuation Analyst: Bearish (confidence: 70%) - expensive
- Warren Buffett Agent: Neutral (confidence: 55%) - outside circle of competence
- Cathie Wood Agent: Bullish (confidence: 92%) - disruptive innovation
- Stanley Druckenmiller Agent: Bullish (confidence: 85%) - growth potential

*Risk Metrics:*
- 30-day volatility: 45% (High)
- Current sector exposure (Tech): 22%
- Recent 6-month return: +85%

**Analysis:**

*Consensus:*
- Bullish signals: 4/7 = 57% (Weak consensus)
- Average confidence: 74%
- Consensus multiplier: 0.75x

*Risk Assessment:*
- Volatility: 45% → Risk factor 0.7x (High volatility)
- Sector: 22% → Adjustment 0.9x (Approaching 25% limit)
- Confidence: 74% → Modifier 1.0x

*Position Calculation:*
```
Base position = 5%
Final position = 5% × 0.75 × 0.7 × 1.0 × 0.9 = 2.36%
```

*Stop Loss:*
- Entry: $480
- Stop: $480 × 0.82 = $393.60 (-18%, wider for high volatility)

**Output:**
```json
{
  "NVDA": {
    "position_size": 2.36,
    "position_sizing_reasoning": "Weak consensus (57%) with mixed signals - growth agents bullish but value agents skeptical. High volatility (45%) requires significant position reduction. Tech sector approaching limit (22/25%). Final position: 2.4% of portfolio.",
    "risk_metrics": {
      "consensus": 57,
      "avg_confidence": 74,
      "volatility": 45,
      "risk_factor": 0.7,
      "sector_exposure": 22,
      "stop_loss": 393.60,
      "max_loss_pct": -18
    },
    "recommendation": "buy",
    "rationale": "Attractive opportunity but high volatility and mixed signals warrant smaller position"
  }
}
```

### Example 3: Low Conviction, Sector Limit (GOOGL)

**Input:**

*Agent Signals:*
- Fundamentals Analyst: Neutral (confidence: 68%)
- Technicals Analyst: Bullish (confidence: 65%)
- Sentiment Analyst: Neutral (confidence: 58%)
- Valuation Analyst: Bullish (confidence: 72%)
- Warren Buffett Agent: Neutral (confidence: 62%)
- Peter Lynch Agent: Bullish (confidence: 70%)

*Risk Metrics:*
- 30-day volatility: 28% (Moderate)
- Current sector exposure (Tech): 24%
- Sector limit: 25%

**Analysis:**

*Consensus:*
- Bullish signals: 3/6 = 50% (Divided)
- Average confidence: 66%
- Consensus multiplier: 0.5x (No clear consensus)

*Risk Assessment:*
- Volatility: 28% → Risk factor 1.0x (Moderate)
- Sector: 24% → Adjustment 0.7x (Near limit)
- Confidence: 66% → Modifier 0.85x

*Position Calculation:*
```
Base position = 5%
Final position = 5% × 0.5 × 1.0 × 0.85 × 0.7 = 1.49%
Approaching minimum threshold (1%)
```

**Output:**
```json
{
  "GOOGL": {
    "position_size": 1.49,
    "position_sizing_reasoning": "Divided agent consensus (50%) indicates uncertainty. Tech sector exposure at 24% is near the 25% limit, requiring significant reduction. Low confidence (66% avg) and lack of conviction leads to minimal position. Consider skipping or waiting for better setup.",
    "risk_metrics": {
      "consensus": 50,
      "avg_confidence": 66,
      "volatility": 28,
      "risk_factor": 1.0,
      "sector_exposure": 24,
      "stop_loss": null,
      "max_loss_pct": -15
    },
    "recommendation": "hold",
    "rationale": "Insufficient conviction and sector limit approaching. Minimal position or skip."
  }
}
```

### Example 4: Bearish Consensus (Hypothetical - DEBT)

**Input:**

*Agent Signals:*
- Fundamentals Analyst: Bearish (confidence: 82%)
- Technicals Analyst: Bearish (confidence: 75%)
- Sentiment Analyst: Bearish (confidence: 78%)
- Valuation Analyst: Neutral (confidence: 55%) - cheap but value trap
- Warren Buffett Agent: Bearish (confidence: 88%) - deteriorating fundamentals
- Michael Burry Agent: Bearish (confidence: 85%) - overleveraged

*Current Position:* 4.5% of portfolio

**Analysis:**

*Consensus:*
- Bearish signals: 5/6 = 83% (Strong bearish consensus)
- Average confidence: 77%
- **Action: Sell/Reduce**

*Risk Assessment:*
- Strong bearish consensus indicates high risk
- Recommendation: Exit position or reduce significantly

**Output:**
```json
{
  "DEBT": {
    "position_size": 0,
    "position_sizing_reasoning": "Strong bearish consensus (83%) across 5/6 agents with average confidence of 77%. Risk Manager recommends exiting current 4.5% position to avoid further losses. Multiple agents flag fundamental deterioration and overleveraging.",
    "risk_metrics": {
      "consensus": -83,
      "avg_confidence": 77,
      "current_position": 4.5,
      "recommended_action": "sell",
      "risk_level": "high"
    },
    "recommendation": "sell",
    "rationale": "Strong bearish consensus indicates significant downside risk. Exit position."
  }
}
```

## Configuration

### Risk Parameters

Default configuration:

```yaml
risk_parameters:
  max_position_size: 10.0           # Max % of portfolio per stock
  min_position_size: 1.0            # Min % of portfolio per stock
  max_sector_exposure: 25.0         # Max % per sector
  target_num_positions: 20          # Target portfolio size
  cash_reserve: 5.0                 # % cash to maintain
  stop_loss_pct: 15.0               # Default stop loss %
  max_portfolio_beta: 1.2           # Max overall portfolio beta
  min_consensus_threshold: 50       # Min % consensus to act
```

### Customizable Parameters

| Parameter | Default | Range | Use Case |
|-----------|---------|-------|----------|
| Max Position | 10% | 5-15% | Lower for conservative, higher for concentrated |
| Target Positions | 20 | 10-30 | Fewer for focus, more for diversification |
| Sector Limit | 25% | 15-35% | Stricter for risk-averse investors |
| Stop Loss | 15% | 10-20% | Tighter for defensive, wider for growth |
| Min Consensus | 50% | 40-70% | Lower for contrarian, higher for conservative |

### Risk Profiles

**Conservative Profile:**
```yaml
max_position_size: 5.0
max_sector_exposure: 20.0
target_num_positions: 25
stop_loss_pct: 12.0
min_consensus_threshold: 65
```

**Balanced Profile:**
```yaml
max_position_size: 8.0
max_sector_exposure: 25.0
target_num_positions: 20
stop_loss_pct: 15.0
min_consensus_threshold: 50
```

**Aggressive Profile:**
```yaml
max_position_size: 12.0
max_sector_exposure: 30.0
target_num_positions: 15
stop_loss_pct: 20.0
min_consensus_threshold: 40
```

## References

### Books

- "Risk Management in Trading" by Davis Edwards
- "The Mathematics of Money Management" by Ralph Vince
- "Portfolio Management Formulas" by Ralph Vince
- "Against the Gods: The Remarkable Story of Risk" by Peter L. Bernstein

### Research Papers

- "Portfolio Selection" - Harry Markowitz (1952) - Nobel Prize winning work on diversification
- "The Kelly Criterion in Blackjack Sports Betting and the Stock Market" - Edward Thorp
- "Convexity and Risk Management" - Various authors on position sizing

### Key Concepts

- **Modern Portfolio Theory**: Diversification reduces risk without sacrificing returns
- **Kelly Criterion**: Optimal position sizing for maximum long-term growth
- **Value at Risk (VaR)**: Statistical measure of potential loss
- **Maximum Drawdown**: Peak-to-trough decline, key risk metric
- **Risk-Adjusted Returns**: Sharpe ratio, Sortino ratio

### Related Agents

- **Portfolio Manager Agent**: Receives risk limits, makes final trading decisions
- **All Analyst Agents**: Provide signals that Risk Manager aggregates
- **All Investor Agents**: Provide qualitative context for risk assessment

## Changelog

### [1.0.0] - 2024-10-18
#### Added
- Initial implementation with consensus-based risk assessment
- Volatility-adjusted position sizing
- Sector concentration limits
- Confidence-weighted scoring
- Stop loss calculations
- Examples: High conviction/low risk, high volatility, sector limits, bearish consensus
- Customizable risk profiles (conservative, balanced, aggressive)

---

**Implementation Reference**: `src/agents/risk_manager.py`

**Status**: ✅ Implemented and Active

**Principle**: *"Risk comes from not knowing what you're doing. The goal of the Risk Manager is to ensure we know exactly what we're doing."*
