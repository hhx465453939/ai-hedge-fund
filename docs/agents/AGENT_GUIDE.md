# Agent Configuration Guide

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Creating Your First Agent](#creating-your-first-agent)
4. [Agent Types Explained](#agent-types-explained)
5. [Working with Analysis Criteria](#working-with-analysis-criteria)
6. [Using LLMs in Agents](#using-llms-in-agents)
7. [Testing and Validation](#testing-and-validation)
8. [Advanced Topics](#advanced-topics)
9. [Troubleshooting](#troubleshooting)
10. [FAQ](#faq)

## Introduction

Welcome to the Agent Configuration Guide! This document will help you create, configure, and customize AI trading agents using the Agent Markdown Specification.

### What Are Agents?

Agents are autonomous modules that analyze stocks and generate trading signals. Each agent embodies a specific investment strategy or analytical approach. Agents work together in a multi-agent system to provide diverse perspectives on investment opportunities.

### Why Use Markdown?

Markdown format offers several advantages:

- **Human-Readable**: Easy to read and understand without special tools
- **Version Control**: Works seamlessly with Git and other VCS
- **Documentation**: Strategy and configuration in one place
- **Shareable**: Can be easily shared and discussed
- **Portable**: Works across different platforms and tools

### Prerequisites

Before creating agents, you should:

- Understand basic Markdown syntax
- Be familiar with investment concepts and metrics
- Know YAML for front matter configuration
- Understand JSON for examples and outputs

## Getting Started

### Understanding the Specification

The [Agent Specification](AGENT_SPEC.md) defines the structure and rules for agent configurations. Key concepts:

- **Front Matter**: YAML metadata at the top of the file
- **Required Sections**: Must-have parts of every agent
- **Signal Format**: Standardized output structure
- **Validation Rules**: Rules that ensure agent correctness

### File Organization

Place your agent configurations in:

```
docs/agents/examples/
├── analyst/
│   ├── fundamentals_analyst.agent.md
│   ├── technicals_analyst.agent.md
│   └── sentiment_analyst.agent.md
├── investor/
│   ├── warren_buffett.agent.md
│   ├── peter_lynch.agent.md
│   └── cathie_wood.agent.md
└── manager/
    ├── risk_manager.agent.md
    └── portfolio_manager.agent.md
```

## Creating Your First Agent

Let's create a simple analyst agent step by step.

### Step 1: Choose Your Strategy

Decide what your agent will focus on. For this example, we'll create a "Growth Momentum Analyst" that looks for companies with strong revenue growth and positive price momentum.

### Step 2: Create the File

Create `growth_momentum_analyst.agent.md`:

```markdown
---
agent_id: "growth_momentum_analyst"
agent_name: "Growth Momentum Analyst"
version: "1.0.0"
agent_type: "analyst"
enabled: true
priority: 5
tags:
  - "growth"
  - "momentum"
  - "technical"
created: "2024-10-18"
updated: "2024-10-18"
---
```

### Step 3: Write the Overview

Describe your agent's purpose:

```markdown
## Overview

The Growth Momentum Analyst identifies stocks with strong revenue growth backed by positive price momentum. This agent combines fundamental growth metrics with technical momentum indicators to find companies in strong uptrends with solid business fundamentals.
```

### Step 4: Define the Strategy

```markdown
## Strategy

### Investment Philosophy

Growth investing combined with momentum analysis provides a powerful edge. Companies with accelerating revenue growth often see continued price appreciation, especially when backed by technical momentum indicators.

### Key Principles

1. **Revenue Growth First**: Focus on top-line growth as the primary indicator
2. **Momentum Confirmation**: Use technical indicators to confirm the trend
3. **Reasonable Valuation**: Avoid excessive valuations even for growth stocks
4. **Risk Management**: Exit when momentum breaks down

### Decision Criteria

- **Primary Factors**: Revenue growth rate, EPS growth, price momentum
- **Secondary Factors**: Valuation ratios, volume trends
- **Risk Considerations**: Volatility, drawdown levels
```

### Step 5: Define Analysis Criteria

```markdown
## Analysis Criteria

### Metrics

#### Growth Metrics (Weight: 40%)

| Metric | Threshold | Signal | Points |
|--------|-----------|--------|--------|
| Revenue Growth YoY | > 20% | bullish | +2 |
| Revenue Growth YoY | 10-20% | neutral | +1 |
| Revenue Growth YoY | < 10% | bearish | 0 |
| EPS Growth YoY | > 25% | bullish | +2 |
| EPS Growth YoY | 10-25% | neutral | +1 |
| EPS Growth YoY | < 10% | bearish | 0 |

#### Momentum Metrics (Weight: 40%)

| Metric | Threshold | Signal | Points |
|--------|-----------|--------|--------|
| 50-day MA | Price > MA | bullish | +2 |
| 200-day MA | Price > MA | bullish | +1 |
| RSI (14-day) | 50-70 | bullish | +1 |
| RSI (14-day) | > 70 | bearish | -1 |
| RSI (14-day) | < 30 | bearish | -1 |

#### Valuation Check (Weight: 20%)

| Metric | Threshold | Signal | Points |
|--------|-----------|--------|--------|
| P/S Ratio | < 10 | bullish | +1 |
| P/S Ratio | > 15 | bearish | -1 |
| PEG Ratio | < 2 | bullish | +1 |

### Scoring System

- **Total Possible Score**: 10 points
- **Bullish Threshold**: >= 7 points
- **Bearish Threshold**: <= 3 points
- **Neutral Range**: 4-6 points

### Confidence Calculation

Confidence = (Actual Score / Max Possible Score) × 100, adjusted by:
- Data completeness factor (reduce by 10% if missing key metrics)
- Consistency factor (increase by 10% if all categories agree)
```

### Step 6: Add Implementation Details

```markdown
## Implementation

### Data Requirements

- **Financial Metrics**: Revenue, EPS, P/S ratio, PEG ratio
- **Market Data**: Daily prices, 50-day MA, 200-day MA
- **Technical Indicators**: RSI (14-day)
- **Time Period**: 1 year of historical data minimum

### Processing Steps

1. **Data Collection**: Fetch financial metrics and price data
2. **Calculate Growth Rates**: YoY revenue and EPS growth
3. **Calculate Technical Indicators**: Moving averages and RSI
4. **Score Each Category**: Apply thresholds and sum points
5. **Generate Signal**: Determine bullish/bearish/neutral based on score
6. **Calculate Confidence**: Apply confidence formula

### Dependencies

- API Requirements: financial_metrics, market_data, technical_indicators
- External Services: None
- Other Agents: None

### Rate Limits

- API Calls per Ticker: ~5 calls
- Total API Calls: 5 × number of tickers
- Execution Time Estimate: 2-3 seconds per ticker
```

### Step 7: Provide Examples

```markdown
## Examples

### Example 1: Strong Growth with Momentum (Bullish)

**Input:**
- Ticker: NVDA
- Date: 2024-01-15
- Revenue Growth: 34%
- EPS Growth: 45%
- Price vs 50-day MA: +8%
- Price vs 200-day MA: +25%
- RSI: 65
- P/S Ratio: 8.5
- PEG Ratio: 1.2

**Analysis:**
- Growth Metrics: +4 points (strong revenue and EPS growth)
- Momentum Metrics: +4 points (above both MAs, RSI in healthy range)
- Valuation Check: +2 points (reasonable P/S and PEG)
- Total Score: 10/10

**Output:**
```json
{
  "NVDA": {
    "signal": "bullish",
    "confidence": 95,
    "reasoning": {
      "growth_metrics": {
        "signal": "bullish",
        "confidence": 100,
        "details": "Revenue Growth: 34%, EPS Growth: 45%"
      },
      "momentum_metrics": {
        "signal": "bullish",
        "confidence": 100,
        "details": "Price above both 50-day and 200-day MA, RSI at 65"
      },
      "valuation_check": {
        "signal": "bullish",
        "confidence": 85,
        "details": "P/S: 8.5, PEG: 1.2 - Reasonable for growth rate"
      }
    }
  }
}
```

### Example 2: Slowing Growth, Weak Momentum (Bearish)

**Input:**
- Ticker: XYZ
- Date: 2024-01-15
- Revenue Growth: 5%
- EPS Growth: 3%
- Price vs 50-day MA: -5%
- Price vs 200-day MA: +2%
- RSI: 35
- P/S Ratio: 12
- PEG Ratio: 3.5

**Analysis:**
- Growth Metrics: 0 points (weak growth)
- Momentum Metrics: +1 point (only above 200-day MA)
- Valuation Check: -2 points (high P/S and PEG for weak growth)
- Total Score: -1/10

**Output:**
```json
{
  "XYZ": {
    "signal": "bearish",
    "confidence": 75,
    "reasoning": {
      "growth_metrics": {
        "signal": "bearish",
        "confidence": 85,
        "details": "Revenue Growth: 5%, EPS Growth: 3% - Below thresholds"
      },
      "momentum_metrics": {
        "signal": "bearish",
        "confidence": 70,
        "details": "Price below 50-day MA, RSI at 35 shows weakness"
      },
      "valuation_check": {
        "signal": "bearish",
        "confidence": 80,
        "details": "P/S: 12, PEG: 3.5 - Expensive for growth rate"
      }
    }
  }
}
```
```

Congratulations! You've created your first agent. Now test it and iterate.

## Agent Types Explained

### Analyst Agents

**Characteristics:**
- Focus on specific analytical dimensions
- Use quantitative metrics and thresholds
- Fast execution (no LLM required)
- Objective, rule-based decisions

**Best For:**
- Technical analysis
- Fundamental screening
- Sentiment analysis
- Valuation metrics

**Example Use Cases:**
- Identify oversold/overbought conditions
- Screen for value stocks
- Analyze earnings growth
- Track insider trading patterns

### Investor Agents

**Characteristics:**
- Emulate famous investors' philosophies
- Use LLMs for nuanced reasoning
- Slower execution (LLM calls)
- Subjective, context-aware decisions

**Best For:**
- Qualitative analysis
- Business quality assessment
- Competitive moat evaluation
- Management quality evaluation

**Example Use Cases:**
- Warren Buffett: Find wonderful companies at fair prices
- Peter Lynch: Identify ten-baggers in everyday businesses
- Cathie Wood: Discover disruptive innovation opportunities

**LLM Configuration for Investor Agents:**

```yaml
model_config:
  provider: "openai"
  model: "gpt-4o"
  temperature: 0.7  # Higher for creative reasoning
  max_tokens: 2000
```

### Manager Agents

**Characteristics:**
- Aggregate inputs from multiple agents
- Make portfolio-level decisions
- Handle risk and position sizing
- Coordinate overall strategy

**Best For:**
- Risk management
- Portfolio construction
- Order execution
- Position sizing

**Example Use Cases:**
- Calculate position sizes based on risk limits
- Aggregate signals from multiple agents
- Set stop losses and take profits
- Rebalance portfolio allocations

## Working with Analysis Criteria

### Choosing Good Metrics

**Characteristics of Good Metrics:**
- ✅ Measurable and objective
- ✅ Available for most stocks
- ✅ Historically predictive
- ✅ Not easily manipulated
- ✅ Updated regularly

**Example Good Metrics:**
- Revenue Growth Rate
- Return on Equity (ROE)
- Debt to Equity Ratio
- Price/Earnings Ratio
- Moving Averages

**Avoid:**
- ❌ Subjective metrics ("good management")
- ❌ Rare or exotic metrics
- ❌ Overfitted indicators
- ❌ Easily manipulated metrics
- ❌ Stale or infrequently updated data

### Setting Thresholds

**Guidelines:**

1. **Research-Based**: Use academic research or industry standards
   - Example: ROE > 15% (commonly cited threshold for quality companies)

2. **Percentile-Based**: Use historical percentiles
   - Example: P/E ratio < 25th percentile for value investing

3. **Relative Measures**: Compare to industry or market averages
   - Example: Operating margin > industry median

4. **Risk-Adjusted**: Consider volatility and drawdown
   - Example: Sharpe ratio > 1.0

5. **Multiple Thresholds**: Use ranges instead of binary cutoffs
   - Example: 
     - Excellent: ROE > 20%
     - Good: ROE 15-20%
     - Fair: ROE 10-15%
     - Poor: ROE < 10%

### Weighting Categories

Assign weights based on importance to your strategy:

```markdown
#### Category 1 (Weight: 40%)
[Highest importance metrics]

#### Category 2 (Weight: 30%)
[Moderate importance metrics]

#### Category 3 (Weight: 20%)
[Supporting metrics]

#### Category 4 (Weight: 10%)
[Minor factors]
```

**Ensure weights sum to 100%**

## Using LLMs in Agents

### When to Use LLMs

**Use LLMs when:**
- ✅ Strategy requires qualitative reasoning
- ✅ Need to interpret narrative information
- ✅ Evaluating business quality or moats
- ✅ Assessing management commentary
- ✅ Understanding industry dynamics

**Don't use LLMs when:**
- ❌ Simple threshold comparisons are sufficient
- ❌ Speed is critical
- ❌ Cost is a major constraint
- ❌ Deterministic outputs are required
- ❌ Calculations can be done programmatically

### Choosing an LLM Provider

| Provider | Best For | Cost | Speed | Quality |
|----------|----------|------|-------|---------|
| OpenAI GPT-4o | Complex reasoning, high quality | $$$ | Medium | Excellent |
| OpenAI GPT-4o-mini | Balanced cost/performance | $ | Fast | Good |
| Anthropic Claude | Long context, analysis | $$ | Medium | Excellent |
| Groq | Speed-critical tasks | $ | Very Fast | Good |
| DeepSeek | Cost optimization | $ | Fast | Good |
| Ollama (Local) | Privacy, no API costs | Free | Slow | Varies |

### LLM Configuration

```yaml
model_config:
  provider: "openai"           # LLM provider
  model: "gpt-4o"              # Specific model
  temperature: 0.7             # Creativity (0.0-1.0)
  max_tokens: 2000             # Response length limit
  top_p: 0.9                   # Nucleus sampling (optional)
  frequency_penalty: 0.0       # Reduce repetition (optional)
  presence_penalty: 0.0        # Encourage diversity (optional)
```

**Temperature Guidelines:**
- `0.0-0.3`: Deterministic, factual analysis
- `0.4-0.7`: Balanced reasoning (recommended)
- `0.8-1.0`: Creative, varied outputs

### Writing Effective Prompts

**Template for Investor Agents:**

```python
prompt = f"""
You are {agent_name}, analyzing {ticker}.

Investment Philosophy:
{philosophy}

Analysis Data:
{data}

Evaluate this stock using your investment criteria:
1. {criterion_1}
2. {criterion_2}
3. {criterion_3}

Provide:
- Overall signal (bullish/bearish/neutral)
- Confidence level (0-100)
- Detailed reasoning for your decision

Format response as JSON.
"""
```

**Best Practices:**
- Be specific about what you want
- Provide relevant context and data
- Ask for structured output (JSON)
- Include examples of good responses
- Set clear decision criteria

## Testing and Validation

### Manual Testing

Test your agent with known scenarios:

```bash
# Test with specific ticker and date
poetry run python src/main.py --ticker AAPL --start-date 2024-01-01 --end-date 2024-01-02
```

**What to Check:**
- Signal output is correct (bullish/bearish/neutral)
- Confidence values are reasonable
- Reasoning is clear and logical
- All metrics are calculated correctly
- Edge cases are handled (missing data, etc.)

### Test Cases

Create test cases in your agent documentation:

```markdown
## Test Cases

### Test 1: High Growth Tech Stock
- Input: NVDA, 2024-Q1 data
- Expected Signal: Bullish
- Expected Confidence: >80%
- Key Metrics: Revenue growth >30%, strong margins

### Test 2: Declining Legacy Company
- Input: XYZ, 2024-Q1 data
- Expected Signal: Bearish
- Expected Confidence: >70%
- Key Metrics: Revenue decline, high debt

### Test 3: Missing Data
- Input: ABC, with incomplete financial data
- Expected: Neutral signal with low confidence
- Behavior: Gracefully handle missing metrics
```

### Validation Checklist

Before deploying your agent:

- [ ] Front matter is valid YAML
- [ ] All required sections are present
- [ ] Agent ID follows naming convention
- [ ] Metrics table is complete
- [ ] Thresholds are justified
- [ ] Examples include expected outputs
- [ ] JSON outputs are valid
- [ ] Signal values are standard (bullish/bearish/neutral)
- [ ] Confidence values are 0-100
- [ ] No hardcoded tickers or dates
- [ ] No API keys or credentials in config
- [ ] Documentation is clear and complete

### Automated Validation

```bash
# Validate agent configuration
poetry run python scripts/validate_agent.py docs/agents/examples/your_agent.agent.md

# Run backtests to validate performance
poetry run python src/backtester.py --ticker AAPL,MSFT,GOOGL --start-date 2023-01-01
```

## Advanced Topics

### Multi-Agent Coordination

Agents can reference outputs from other agents:

```yaml
---
agent_id: "combined_value_momentum"
# ...
dependencies:
  - "fundamentals_analyst"
  - "technicals_analyst"
---
```

### Dynamic Thresholds

Adjust thresholds based on market conditions:

```markdown
#### Adaptive P/E Threshold

- **Bull Market**: P/E < 25 (more lenient)
- **Neutral Market**: P/E < 20
- **Bear Market**: P/E < 15 (more strict)
```

### Custom Scoring Functions

Define non-linear scoring:

```markdown
### Advanced Scoring

Score = (Growth_Score × 0.4) + (Quality_Score × 0.3) + (Momentum_Score × 0.3)

Where:
- Growth_Score = min(Revenue_Growth / 0.20, 1.0) × 100
- Quality_Score = (ROE / 0.15) × 0.5 + (Debt_Ratio_Inverse) × 0.5
- Momentum_Score = Normalized RSI × 0.6 + Price_vs_MA × 0.4
```

### Sector-Specific Agents

Create agents tailored to specific sectors:

```markdown
---
agent_id: "tech_growth_analyst"
# ...
tags:
  - "technology"
  - "software"
  - "saas"
sector_filter: ["Information Technology", "Communication Services"]
---

## Strategy

Specialized metrics for technology companies:
- Rule of 40 (Growth Rate + Profit Margin)
- Customer Acquisition Cost (CAC) Ratio
- Net Dollar Retention
- Free Cash Flow Conversion
```

### Ensemble Methods

Combine multiple agents with voting or weighting:

```markdown
---
agent_id: "ensemble_investor"
agent_type: "manager"
# ...
---

## Strategy

Aggregate signals from multiple investor agents:

### Voting Method

- Collect signals from: Buffett, Lynch, Munger, Fisher
- Bullish: >= 3 bullish signals
- Bearish: >= 3 bearish signals
- Neutral: Otherwise

### Weighted Average

- Buffett: 30%
- Lynch: 25%
- Munger: 25%
- Fisher: 20%

Calculate weighted confidence and combined reasoning.
```

## Troubleshooting

### Common Issues

#### Issue: Agent Returns Neutral for All Stocks

**Causes:**
- Thresholds too strict or too loose
- Missing data not handled properly
- Scoring ranges misconfigured

**Solutions:**
- Review threshold values against real data
- Add fallback logic for missing data
- Adjust scoring system (check min/max scores)
- Test with known good/bad examples

#### Issue: Low Confidence Scores

**Causes:**
- Too many conflicting metrics
- Insufficient data
- Confidence formula too conservative

**Solutions:**
- Review metric coherence
- Increase data coverage
- Adjust confidence calculation
- Weight metrics by reliability

#### Issue: LLM Agent Takes Too Long

**Causes:**
- Complex prompts
- Large max_tokens setting
- Slow provider

**Solutions:**
- Simplify prompt
- Reduce max_tokens to 1000-1500
- Switch to faster provider (Groq)
- Use caching for repeated queries

#### Issue: Inconsistent Signals

**Causes:**
- LLM temperature too high
- Non-deterministic logic
- Data race conditions

**Solutions:**
- Lower temperature to 0.3-0.5
- Add explicit decision rules
- Ensure consistent data snapshots

### Debugging Tips

1. **Add Detailed Logging**
   ```python
   progress.update_status(agent_id, ticker, "Analyzing", 
                          analysis=json.dumps(debug_data, indent=2))
   ```

2. **Test with Known Data**
   - Use specific dates with known outcomes
   - Compare against manual calculations

3. **Validate Data Quality**
   - Check for null/missing values
   - Verify data freshness
   - Confirm API responses

4. **Review Examples**
   - Compare actual output with expected examples
   - Check edge cases

5. **Simplify First**
   - Start with simple version
   - Add complexity incrementally
   - Test after each addition

## FAQ

### General Questions

**Q: Can I create an agent without using LLMs?**

A: Yes! Analyst agents typically don't use LLMs. They use rule-based logic and thresholds. See `fundamentals_analyst.agent.md` for an example.

**Q: How many metrics should an agent use?**

A: Generally 8-15 metrics across 3-5 categories. Too few lacks nuance; too many causes confusion and conflicts.

**Q: What's a good confidence threshold for taking action?**

A: Most agents use:
- Very High confidence (>80%): Strong signal, larger positions
- High confidence (60-80%): Moderate conviction
- Low confidence (<60%): Small positions or skip

**Q: How do I handle missing data?**

A: Options:
1. Skip the metric (reduce max score)
2. Use industry average as proxy
3. Signal neutral for that category
4. Require minimum data completeness

**Q: Can agents trade in real-time?**

A: The system is designed for analysis, not real trading. Agents evaluate stocks but don't execute trades.

**Q: How often should agents be updated?**

A: 
- Data updates: As new data becomes available (daily/weekly)
- Logic updates: When strategy evolves or new research emerges
- Threshold updates: Quarterly or semi-annually based on market regime

### Technical Questions

**Q: What Python version is required?**

A: Python 3.11+ (see pyproject.toml)

**Q: Which API keys do I need?**

A: Minimum:
- One LLM API key (OpenAI, Anthropic, etc.) for investor agents
- FINANCIAL_DATASETS_API_KEY for stock data (optional for AAPL, GOOGL, MSFT, NVDA, TSLA)

**Q: How do I add a custom metric?**

A: 
1. Add data collection logic in agent implementation
2. Define metric in Analysis Criteria section
3. Add to scoring logic
4. Update examples with the new metric

**Q: Can agents use external APIs?**

A: Yes, document in Implementation > Dependencies section. Be mindful of:
- Rate limits
- API costs
- Data latency
- Error handling

**Q: How do I version control agents?**

A: 
- Use semantic versioning in front matter
- Update `updated` date on changes
- Maintain changelog section
- Git commit with clear messages

**Q: Can agents be run in parallel?**

A: Yes, most analyst agents can run concurrently. Manager agents typically run after analysts complete.

### Strategy Questions

**Q: What if my agent disagrees with others?**

A: This is expected and valuable! Diverse perspectives reduce bias. The portfolio manager aggregates all signals.

**Q: Should I backtest my agent?**

A: Absolutely! Run backtests to validate:
```bash
poetry run python src/backtester.py --ticker AAPL,MSFT,GOOGL --start-date 2023-01-01
```

**Q: How do I choose between investor and analyst agent type?**

A: 
- **Analyst**: Quantitative, objective metrics, fast execution
- **Investor**: Qualitative reasoning, business analysis, slower execution

**Q: Can I combine multiple strategies in one agent?**

A: You can, but it's better to:
- Create separate agents for each strategy
- Let the portfolio manager combine them
- Keep agents focused and coherent

**Q: What makes a good agent?**

A: 
- ✅ Clear, coherent strategy
- ✅ Justified thresholds
- ✅ Consistent performance
- ✅ Well-documented reasoning
- ✅ Handles edge cases
- ✅ Complementary to other agents

## Additional Resources

### Example Agents

Review these examples in the `examples/` directory:

- `fundamentals_analyst.agent.md` - Rule-based fundamental analysis
- `warren_buffett.agent.md` - LLM-based investor agent
- `growth_momentum_analyst.agent.md` - Combined fundamental/technical
- `risk_manager.agent.md` - Manager agent example

### Further Reading

**Investment Strategy:**
- "The Intelligent Investor" by Benjamin Graham
- "Common Stocks and Uncommon Profits" by Philip Fisher
- "One Up On Wall Street" by Peter Lynch

**Technical Analysis:**
- "Technical Analysis of the Financial Markets" by John Murphy
- "Evidence-Based Technical Analysis" by David Aronson

**Quantitative Finance:**
- "Quantitative Trading" by Ernest Chan
- "Advances in Financial Machine Learning" by Marcos López de Prado

**Multi-Agent Systems:**
- LangGraph Documentation: https://langchain-ai.github.io/langgraph/
- LangChain Documentation: https://python.langchain.com/

### Community

- GitHub Issues: Report bugs or request features
- Discussions: Share strategies and ask questions
- Pull Requests: Contribute new agents or improvements

---

**Happy Agent Building!** 🚀

For questions or feedback, please open an issue on GitHub.

*Last Updated: 2024-10-18*
