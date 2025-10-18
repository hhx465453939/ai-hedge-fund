---
agent_id: "fundamentals_analyst_agent"
agent_name: "Fundamentals Analyst"
version: "1.0.0"
agent_type: "analyst"
enabled: true
priority: 5
tags:
  - "fundamental"
  - "value"
  - "financial-health"
  - "quantitative"
created: "2024-10-18"
updated: "2024-10-18"
---

## Overview

The Fundamentals Analyst is a quantitative agent that analyzes a company's financial health, profitability, growth, and valuation using key fundamental metrics. This agent provides objective, rule-based assessments without requiring LLM inference, making it fast and cost-effective for screening stocks based on financial fundamentals.

## Strategy

### Investment Philosophy

Strong fundamentals are the bedrock of successful investing. Companies with solid profitability metrics, healthy balance sheets, consistent growth, and reasonable valuations tend to outperform over the long term. This agent focuses on identifying companies that demonstrate financial strength across multiple dimensions.

### Key Principles

1. **Profitability First**: Companies must demonstrate strong returns on equity and healthy profit margins
2. **Financial Health**: Conservative debt levels and strong liquidity are essential
3. **Growth Matters**: Consistent revenue and earnings growth indicate business momentum
4. **Valuation Discipline**: Even great companies can be poor investments if overpriced

### Decision Criteria

- **Primary Factors**: ROE, net margin, operating margin, revenue growth, earnings growth
- **Secondary Factors**: Current ratio, debt-to-equity, free cash flow
- **Risk Considerations**: Valuation ratios (P/E, P/B, P/S) to avoid overpaying

## Analysis Criteria

### Metrics

#### Profitability Analysis (Weight: 30%)

| Metric | Threshold | Signal | Points |
|--------|-----------|--------|--------|
| Return on Equity (ROE) | > 15% | bullish | +2 |
| Return on Equity (ROE) | 0-15% | neutral | 0 |
| Return on Equity (ROE) | < 0% | bearish | -2 |
| Net Profit Margin | > 20% | bullish | +2 |
| Net Profit Margin | 10-20% | neutral | +1 |
| Net Profit Margin | < 10% | bearish | 0 |
| Operating Margin | > 15% | bullish | +2 |
| Operating Margin | 5-15% | neutral | +1 |
| Operating Margin | < 5% | bearish | 0 |

**Category Score**: 0-6 points
- Bullish: >= 4 points (strong profitability)
- Neutral: 2-3 points (moderate profitability)
- Bearish: <= 1 point (weak profitability)

#### Growth Analysis (Weight: 25%)

| Metric | Threshold | Signal | Points |
|--------|-----------|--------|--------|
| Revenue Growth YoY | > 10% | bullish | +2 |
| Revenue Growth YoY | 0-10% | neutral | +1 |
| Revenue Growth YoY | < 0% | bearish | 0 |
| Earnings Growth YoY | > 10% | bullish | +2 |
| Earnings Growth YoY | 0-10% | neutral | +1 |
| Earnings Growth YoY | < 0% | bearish | 0 |
| Book Value Growth YoY | > 10% | bullish | +1 |
| Book Value Growth YoY | 0-10% | neutral | 0 |
| Book Value Growth YoY | < 0% | bearish | -1 |

**Category Score**: 0-5 points
- Bullish: >= 4 points (strong growth)
- Neutral: 2-3 points (moderate growth)
- Bearish: <= 1 point (weak/negative growth)

#### Financial Health (Weight: 25%)

| Metric | Threshold | Signal | Points |
|--------|-----------|--------|--------|
| Current Ratio | > 1.5 | bullish | +1 |
| Current Ratio | 1.0-1.5 | neutral | 0 |
| Current Ratio | < 1.0 | bearish | -1 |
| Debt to Equity | < 0.5 | bullish | +1 |
| Debt to Equity | 0.5-1.0 | neutral | 0 |
| Debt to Equity | > 1.0 | bearish | -1 |
| FCF per Share vs EPS | FCF > 80% of EPS | bullish | +1 |
| FCF per Share vs EPS | FCF 50-80% of EPS | neutral | 0 |
| FCF per Share vs EPS | FCF < 50% of EPS | bearish | -1 |

**Category Score**: -3 to +3 points
- Bullish: >= 2 points (strong financial health)
- Neutral: 0-1 points (adequate health)
- Bearish: <= -1 points (weak balance sheet)

#### Valuation Ratios (Weight: 20%)

| Metric | Threshold | Signal | Points |
|--------|-----------|--------|--------|
| P/E Ratio | < 15 | bullish | +1 |
| P/E Ratio | 15-25 | neutral | 0 |
| P/E Ratio | > 25 | bearish | -1 |
| P/B Ratio | < 2 | bullish | +1 |
| P/B Ratio | 2-3 | neutral | 0 |
| P/B Ratio | > 3 | bearish | -1 |
| P/S Ratio | < 3 | bullish | +1 |
| P/S Ratio | 3-5 | neutral | 0 |
| P/S Ratio | > 5 | bearish | -1 |

**Category Score**: -3 to +3 points
- Bullish: >= 2 points (attractive valuation)
- Neutral: 0-1 points (fair valuation)
- Bearish: <= -1 points (expensive valuation)

### Scoring System

- **Maximum Category Scores**: Profitability (6) + Growth (5) + Health (3) + Valuation (3) = 17 points
- **Signal Determination**: Based on bullish vs bearish category count
  - Bullish: >= 3 bullish categories
  - Bearish: >= 3 bearish categories
  - Neutral: Otherwise

### Confidence Calculation

```
Confidence = (max(bullish_categories, bearish_categories) / total_categories) × 100

Adjustments:
- Strong consensus (all categories agree): +10%
- Mixed signals (equal bull/bear): -10%
- Missing key data (>2 metrics unavailable): -20%
```

## Implementation

### Data Requirements

- **Financial Metrics**: 
  - Income Statement: Revenue, Net Income, Operating Income, EPS
  - Balance Sheet: Current Assets, Current Liabilities, Total Debt, Shareholders Equity
  - Cash Flow: Free Cash Flow
  - Ratios: ROE, Current Ratio, Debt-to-Equity
  - Valuation: P/E, P/B, P/S ratios
- **Time Period**: TTM (Trailing Twelve Months) data, plus prior year for growth calculations
- **History**: Minimum 2 years to calculate YoY growth rates

### Processing Steps

1. **Data Collection**: 
   - Fetch financial metrics via `get_financial_metrics(ticker, end_date, period="ttm", limit=10)`
   - Extract most recent metrics (index 0)
   - Store previous year metrics (index 4 for quarterly, index 1 for annual) for growth calculations

2. **Profitability Analysis**:
   - Extract ROE, net margin, operating margin
   - Apply thresholds for each metric
   - Sum points to get profitability score
   - Determine category signal (bullish/neutral/bearish)

3. **Growth Analysis**:
   - Calculate YoY revenue growth
   - Calculate YoY earnings growth  
   - Calculate YoY book value growth
   - Apply thresholds and score

4. **Financial Health Analysis**:
   - Check current ratio for liquidity
   - Evaluate debt-to-equity ratio
   - Compare FCF per share to EPS
   - Score and categorize

5. **Valuation Analysis**:
   - Evaluate P/E, P/B, P/S ratios
   - Apply relative thresholds
   - Note: Lower ratios are generally better (opposite scoring)

6. **Signal Generation**:
   - Count bullish vs bearish categories
   - Determine overall signal
   - Calculate confidence based on consensus

7. **Output Formatting**:
   - Structure reasoning by category
   - Include specific metric values
   - Return standardized JSON

### Dependencies

- **API Requirements**: 
  - `financial_metrics` endpoint from Financial Datasets API
  - TTM period data with 10+ periods history
- **External Services**: None
- **Other Agents**: None (independent analyst)

### Rate Limits

- **API Calls per Ticker**: 1 call to get_financial_metrics
- **Total API Calls**: 1 × number of tickers
- **Execution Time Estimate**: 1-2 seconds per ticker

### Error Handling

- **Missing Metrics**: Skip metric, adjust max score, note in reasoning
- **Invalid Data**: Treat as missing, use neutral scoring
- **API Failures**: Return neutral signal with low confidence, log error
- **Calculation Errors**: Default to neutral, include error details in reasoning

## Examples

### Example 1: Strong Fundamentals (Apple - AAPL)

**Input:**
- Ticker: AAPL
- Date: 2024-01-15
- Financial Metrics (TTM):
  - ROE: 147.25%
  - Net Margin: 25.31%
  - Operating Margin: 29.82%
  - Revenue Growth: 11.2%
  - EPS Growth: 16.3%
  - Current Ratio: 1.02
  - Debt to Equity: 1.97
  - P/E Ratio: 28.5
  - P/B Ratio: 42.0
  - P/S Ratio: 7.2

**Analysis:**

*Profitability:*
- ROE: 147.25% → +2 points (exceptional)
- Net Margin: 25.31% → +2 points (strong)
- Operating Margin: 29.82% → +2 points (excellent)
- **Total: 6/6 points → Bullish**

*Growth:*
- Revenue Growth: 11.2% → +2 points (solid)
- EPS Growth: 16.3% → +2 points (strong)
- Book Value Growth: N/A → 0 points
- **Total: 4/5 points → Bullish**

*Financial Health:*
- Current Ratio: 1.02 → 0 points (adequate but not strong)
- Debt to Equity: 1.97 → -1 point (elevated debt)
- FCF/EPS: 85% → +1 point (strong cash generation)
- **Total: 0/3 points → Neutral**

*Valuation:*
- P/E: 28.5 → -1 point (premium)
- P/B: 42.0 → -1 point (very high)
- P/S: 7.2 → -1 point (elevated)
- **Total: -3/3 points → Bearish**

**Signal Determination:**
- Bullish categories: 2 (Profitability, Growth)
- Neutral categories: 1 (Financial Health)
- Bearish categories: 1 (Valuation)
- **Overall Signal: Neutral** (no clear majority, but strong fundamentals offset by high valuation)

**Output:**
```json
{
  "AAPL": {
    "signal": "neutral",
    "confidence": 65,
    "reasoning": {
      "profitability_signal": {
        "signal": "bullish",
        "details": "ROE: 147.25%, Net Margin: 25.31%, Op Margin: 29.82%"
      },
      "growth_signal": {
        "signal": "bullish",
        "details": "Revenue Growth: 11.2%, Earnings Growth: 16.3%"
      },
      "financial_health_signal": {
        "signal": "neutral",
        "details": "Current Ratio: 1.02, D/E: 1.97"
      },
      "price_ratios_signal": {
        "signal": "bearish",
        "details": "P/E: 28.5, P/B: 42.0, P/S: 7.2"
      }
    }
  }
}
```

### Example 2: Weak Fundamentals (Hypothetical Company - WEAK)

**Input:**
- Ticker: WEAK
- Date: 2024-01-15
- Financial Metrics (TTM):
  - ROE: 4.2%
  - Net Margin: 6.8%
  - Operating Margin: 8.3%
  - Revenue Growth: -3.5%
  - EPS Growth: -12.1%
  - Book Value Growth: -8.2%
  - Current Ratio: 0.85
  - Debt to Equity: 2.45
  - FCF per Share: $0.45
  - EPS: $1.20
  - P/E Ratio: 18.5
  - P/B Ratio: 0.9
  - P/S Ratio: 0.6

**Analysis:**

*Profitability:*
- ROE: 4.2% → 0 points (weak)
- Net Margin: 6.8% → 0 points (below threshold)
- Operating Margin: 8.3% → +1 point (marginal)
- **Total: 1/6 points → Bearish**

*Growth:*
- Revenue Growth: -3.5% → 0 points (declining)
- EPS Growth: -12.1% → 0 points (declining)
- Book Value Growth: -8.2% → -1 point (declining)
- **Total: -1/5 points → Bearish**

*Financial Health:*
- Current Ratio: 0.85 → -1 point (liquidity concern)
- Debt to Equity: 2.45 → -1 point (high leverage)
- FCF/EPS: 37.5% → -1 point (poor conversion)
- **Total: -3/3 points → Bearish**

*Valuation:*
- P/E: 18.5 → 0 points (fair for weak fundamentals)
- P/B: 0.9 → +1 point (below book value)
- P/S: 0.6 → +1 point (low multiple)
- **Total: +2/3 points → Bullish** (cheap valuation)

**Signal Determination:**
- Bullish categories: 1 (Valuation - distressed pricing)
- Bearish categories: 3 (Profitability, Growth, Financial Health)
- **Overall Signal: Bearish** (fundamental weakness overwhelms cheap valuation)

**Output:**
```json
{
  "WEAK": {
    "signal": "bearish",
    "confidence": 85,
    "reasoning": {
      "profitability_signal": {
        "signal": "bearish",
        "details": "ROE: 4.2%, Net Margin: 6.8%, Op Margin: 8.3%"
      },
      "growth_signal": {
        "signal": "bearish",
        "details": "Revenue Growth: -3.5%, Earnings Growth: -12.1%"
      },
      "financial_health_signal": {
        "signal": "bearish",
        "details": "Current Ratio: 0.85, D/E: 2.45"
      },
      "price_ratios_signal": {
        "signal": "bullish",
        "details": "P/E: 18.5, P/B: 0.9, P/S: 0.6"
      }
    }
  }
}
```

### Example 3: Mixed Signals (Hypothetical Company - MIXED)

**Input:**
- Ticker: MIXED
- Date: 2024-01-15
- Financial Metrics (TTM):
  - ROE: 18.5%
  - Net Margin: 14.2%
  - Operating Margin: 16.8%
  - Revenue Growth: 8.5%
  - EPS Growth: 6.2%
  - Current Ratio: 1.85
  - Debt to Equity: 0.35
  - FCF per Share: $2.10
  - EPS: $2.50
  - P/E Ratio: 22.0
  - P/B Ratio: 2.8
  - P/S Ratio: 4.5

**Analysis:**

*Profitability:*
- ROE: 18.5% → +2 points (good)
- Net Margin: 14.2% → +1 point (decent)
- Operating Margin: 16.8% → +2 points (strong)
- **Total: 5/6 points → Bullish**

*Growth:*
- Revenue Growth: 8.5% → +1 point (moderate)
- EPS Growth: 6.2% → +1 point (moderate)
- Book Value Growth: N/A → 0 points
- **Total: 2/5 points → Neutral**

*Financial Health:*
- Current Ratio: 1.85 → +1 point (good liquidity)
- Debt to Equity: 0.35 → +1 point (conservative)
- FCF/EPS: 84% → +1 point (strong conversion)
- **Total: +3/3 points → Bullish**

*Valuation:*
- P/E: 22.0 → 0 points (fair)
- P/B: 2.8 → 0 points (fair)
- P/S: 4.5 → 0 points (fair)
- **Total: 0/3 points → Neutral**

**Signal Determination:**
- Bullish categories: 2 (Profitability, Financial Health)
- Neutral categories: 2 (Growth, Valuation)
- Bearish categories: 0
- **Overall Signal: Bullish** (solid fundamentals with fair valuation)

**Output:**
```json
{
  "MIXED": {
    "signal": "bullish",
    "confidence": 70,
    "reasoning": {
      "profitability_signal": {
        "signal": "bullish",
        "details": "ROE: 18.5%, Net Margin: 14.2%, Op Margin: 16.8%"
      },
      "growth_signal": {
        "signal": "neutral",
        "details": "Revenue Growth: 8.5%, Earnings Growth: 6.2%"
      },
      "financial_health_signal": {
        "signal": "bullish",
        "details": "Current Ratio: 1.85, D/E: 0.35"
      },
      "price_ratios_signal": {
        "signal": "neutral",
        "details": "P/E: 22.0, P/B: 2.8, P/S: 4.5"
      }
    }
  }
}
```

## Configuration

### Parameters

All thresholds are defined in the Analysis Criteria section and can be adjusted based on:
- Market conditions (bull vs bear)
- Sector characteristics (growth vs value)
- Investment time horizon
- Risk tolerance

### Customization Options

| Parameter | Current Default | Alternative Values | Use Case |
|-----------|----------------|-------------------|----------|
| ROE Threshold | 15% | 12-20% | Lower for cyclicals, higher for tech |
| Debt/Equity Max | 0.5 | 0.3-1.0 | More strict for financials |
| Revenue Growth Min | 10% | 5-15% | Lower for mature, higher for growth |
| P/E Max (bullish) | 15 | 10-20 | Adjust for sector norms |

### Environment Variables

- `FINANCIAL_DATASETS_API_KEY`: Required for non-free tickers

## References

### Books

- "Security Analysis" by Benjamin Graham and David Dodd (1934)
- "Financial Statement Analysis" by Martin Fridson and Fernando Alvarez
- "The Intelligent Investor" by Benjamin Graham

### Research Papers

- "The Cross-Section of Expected Stock Returns" - Fama & French (1992)
- "Profitability, Investment, and Average Returns" - Novy-Marx (2013)

### Metrics Resources

- Investopedia: Financial Ratios Reference
- CFA Institute: Financial Analysis and Reporting Standards
- AICPA: Financial Reporting Framework

## Changelog

### [1.0.0] - 2024-10-18
#### Added
- Initial release with 4 analysis categories
- Profitability, Growth, Financial Health, Valuation analysis
- Comprehensive metric thresholds
- Example scenarios for strong, weak, and mixed fundamentals
- Confidence calculation with adjustment factors

---

**Implementation Reference**: `src/agents/fundamentals.py`

**Status**: ✅ Implemented and Active
