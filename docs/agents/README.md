# Agent Documentation

Welcome to the AI Hedge Fund Agent Documentation! This directory contains comprehensive documentation for creating, configuring, and understanding trading agents.

## 📚 Documentation Structure

### Core Documentation

- **[AGENT_SPEC.md](AGENT_SPEC.md)** - The formal Agent Markdown Specification (v1.0)
  - Complete specification for agent configuration format
  - Required sections and validation rules
  - Signal format standards
  - Best practices and conventions

- **[AGENT_GUIDE.md](AGENT_GUIDE.md)** - Practical guide for creating agents
  - Step-by-step tutorials
  - Agent type explanations
  - LLM integration guide
  - Testing and debugging tips
  - FAQ and troubleshooting

### Example Agents

The `examples/` directory contains fully documented agent configurations:

#### Analyst Agents
- **[fundamentals_analyst.agent.md](examples/fundamentals_analyst.agent.md)** - Rule-based fundamental analysis
  - Analyzes profitability, growth, financial health, and valuation
  - Fast execution, no LLM required
  - 4 categories, 17-point scoring system

#### Investor Agents
- **[warren_buffett.agent.md](examples/warren_buffett.agent.md)** - Value investing legend
  - Seeks wonderful companies at fair prices
  - Focuses on moats, management quality, and intrinsic value
  - LLM-powered qualitative reasoning
  - 52-point quantitative scoring + qualitative assessment

#### Manager Agents
- **[risk_manager.agent.md](examples/risk_manager.agent.md)** - Portfolio risk management
  - Aggregates signals from all agents
  - Calculates position sizes based on consensus and volatility
  - Enforces sector limits and diversification rules
  - Sets stop losses and risk limits

## 🚀 Quick Start

### For New Users

1. **Read the basics**:
   ```bash
   Start with AGENT_GUIDE.md → "Getting Started" section
   ```

2. **Review an example**:
   ```bash
   Read examples/fundamentals_analyst.agent.md to see a complete agent
   ```

3. **Create your first agent**:
   ```bash
   Follow the tutorial in AGENT_GUIDE.md → "Creating Your First Agent"
   ```

### For Experienced Users

1. **Reference the spec**: Use [AGENT_SPEC.md](AGENT_SPEC.md) as your authoritative reference

2. **Clone an example**: Copy and modify an existing agent from `examples/`

3. **Validate your agent**: Check against validation rules in the spec

## 📖 Understanding Agents

### Agent Types

| Type | Purpose | LLM Required | Speed | Examples |
|------|---------|--------------|-------|----------|
| **Analyst** | Quantitative analysis of specific dimensions | No | Fast | Fundamentals, Technicals, Sentiment |
| **Investor** | Emulate famous investors' philosophies | Yes | Slower | Warren Buffett, Peter Lynch, Cathie Wood |
| **Manager** | Portfolio-level decisions and risk management | Optional | Medium | Risk Manager, Portfolio Manager |

### Agent Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                     Trading Decision Flow                    │
└─────────────────────────────────────────────────────────────┘

1. ANALYST AGENTS (Parallel Execution)
   ├─ Fundamentals Analyst → Evaluates financial metrics
   ├─ Technicals Analyst   → Analyzes price trends
   ├─ Sentiment Analyst    → Gauges market sentiment
   └─ Valuation Analyst    → Assesses pricing

2. INVESTOR AGENTS (Parallel Execution)
   ├─ Warren Buffett  → Quality + fair price
   ├─ Peter Lynch     → Growth at reasonable price
   ├─ Charlie Munger  → Wonderful businesses
   └─ Others...       → Various strategies

3. RISK MANAGER (Aggregation)
   └─ Aggregates all signals
      Calculates consensus
      Assesses risk metrics
      Sets position sizes

4. PORTFOLIO MANAGER (Final Decision)
   └─ Reviews risk limits
      Makes final buy/sell decisions
      Generates orders
```

## 🎯 Common Use Cases

### I want to create a new analyst agent

1. Read: [AGENT_GUIDE.md](AGENT_GUIDE.md) → "Creating Your First Agent"
2. Reference: [examples/fundamentals_analyst.agent.md](examples/fundamentals_analyst.agent.md)
3. Focus on: Defining clear metrics and thresholds
4. Skip: LLM configuration (not needed for analysts)

### I want to create an investor agent

1. Read: [AGENT_GUIDE.md](AGENT_GUIDE.md) → "Using LLMs in Agents"
2. Reference: [examples/warren_buffett.agent.md](examples/warren_buffett.agent.md)
3. Focus on: Investment philosophy and qualitative criteria
4. Include: LLM configuration in front matter

### I want to understand risk management

1. Read: [examples/risk_manager.agent.md](examples/risk_manager.agent.md)
2. Focus on: Position sizing formulas and risk constraints
3. Customize: Risk parameters for your risk tolerance

### I want to modify an existing agent

1. Read: The agent's `.agent.md` file in `examples/`
2. Understand: The strategy and metrics
3. Modify: Thresholds or weights based on your needs
4. Update: Version number and updated date
5. Test: Run backtests to validate changes

## 📋 Validation Checklist

Before deploying an agent, ensure:

- [ ] Front matter is valid YAML with all required fields
- [ ] All required sections are present (Overview, Strategy, Analysis Criteria, Implementation, Examples)
- [ ] Agent ID follows naming convention (`lowercase_with_underscores`)
- [ ] Version follows semantic versioning (X.Y.Z)
- [ ] Metrics tables include all required columns
- [ ] At least one complete example with expected output
- [ ] JSON outputs are valid and follow signal format
- [ ] Signal values are standard (bullish/bearish/neutral)
- [ ] Confidence values are 0-100
- [ ] No hardcoded tickers or dates in logic
- [ ] No API keys or credentials in configuration
- [ ] Documentation is clear and complete

## 🧪 Testing Your Agent

### Manual Testing

```bash
# Test with specific ticker
poetry run python src/main.py --ticker AAPL --start-date 2024-01-01 --end-date 2024-01-02

# Test with multiple tickers
poetry run python src/main.py --ticker AAPL,MSFT,GOOGL --start-date 2024-01-01 --end-date 2024-01-02
```

### Backtesting

```bash
# Run backtest to validate performance
poetry run python src/backtester.py --ticker AAPL,MSFT,GOOGL --start-date 2023-01-01 --end-date 2024-01-01
```

### Validation Script

```bash
# Validate agent configuration (when implemented)
poetry run python scripts/validate_agent.py docs/agents/examples/your_agent.agent.md
```

## 🔧 Customization

### Adjusting Thresholds

All agents can be customized by adjusting thresholds in the Analysis Criteria section:

```markdown
| Metric | Threshold | Signal | Points |
|--------|-----------|--------|--------|
| ROE    | > 15%     | bullish | +2    |  ← Adjust this threshold
```

**When to adjust:**
- Different market conditions (bull vs bear)
- Sector-specific norms (tech vs utilities)
- Risk tolerance changes
- Time horizon changes

### Changing Weights

Adjust category weights to emphasize different factors:

```markdown
#### Profitability (Weight: 40%)  ← Increase/decrease weight
```

Ensure all weights sum to 100%.

### LLM Configuration

For investor agents, adjust LLM parameters:

```yaml
model_config:
  provider: "openai"
  model: "gpt-4o"              # or gpt-4o-mini for cost savings
  temperature: 0.7             # 0.5-0.9 range
  max_tokens: 2000             # 1000-2000 typical
```

## 📚 Additional Resources

### Learn Investment Strategies

- **Value Investing**: Read Warren Buffett and Ben Graham examples
- **Growth Investing**: Read Peter Lynch and Cathie Wood examples
- **Quantitative**: Read analyst agent examples (Fundamentals, Technicals)

### Technical Documentation

- **LangGraph**: https://langchain-ai.github.io/langgraph/
- **LangChain**: https://python.langchain.com/
- **Financial Datasets API**: Refer to API documentation

### Community

- **GitHub Issues**: Report bugs or request features
- **Discussions**: Share strategies and ask questions
- **Pull Requests**: Contribute new agents

## 🤝 Contributing

We welcome contributions! To add a new agent:

1. **Create the agent configuration** following the specification
2. **Add comprehensive examples** (at least 2-3 scenarios)
3. **Document thoroughly** - clear explanations and rationale
4. **Test your agent** - run backtests to validate
5. **Submit a pull request** with your `.agent.md` file

### Contribution Guidelines

- Follow the [Agent Specification](AGENT_SPEC.md) exactly
- Provide clear rationale for all thresholds
- Include detailed examples with expected outputs
- Document any novel techniques or approaches
- Keep agents focused on a coherent strategy
- Avoid duplicate strategies

## 🐛 Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| Agent returns neutral for all stocks | Review thresholds - may be too strict/loose |
| Low confidence scores | Check if metrics are conflicting |
| LLM agent too slow | Use faster provider (Groq) or smaller model |
| Missing data errors | Add fallback logic for missing metrics |
| Validation errors | Check YAML syntax and required fields |

See [AGENT_GUIDE.md](AGENT_GUIDE.md) → "Troubleshooting" for detailed solutions.

## 📞 Support

- **Documentation Issues**: Open issue on GitHub
- **Agent Questions**: See [AGENT_GUIDE.md](AGENT_GUIDE.md) → "FAQ"
- **Bugs**: Report on GitHub Issues with agent configuration and error details

## 🗺️ Roadmap

Future enhancements planned:

- [ ] Automated agent validation script
- [ ] Agent performance tracking and analytics
- [ ] Web UI for agent configuration
- [ ] Agent marketplace/repository
- [ ] Multi-language support for agent configs
- [ ] Advanced backtesting with agent attribution
- [ ] Agent A/B testing framework

## 📜 License

This documentation and example agents are part of the AI Hedge Fund project, licensed under MIT License.

---

**Version**: 1.0.0  
**Last Updated**: 2024-10-18  
**Maintained by**: AI Hedge Fund Contributors

For questions or suggestions, please open an issue on GitHub.
