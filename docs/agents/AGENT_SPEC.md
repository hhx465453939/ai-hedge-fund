# Agent Markdown Specification (v1.0)

## Overview

This specification defines a standardized Markdown format for configuring AI trading agents in the AI Hedge Fund system. Agents defined using this specification can analyze stocks and generate trading signals based on various investment strategies and philosophies.

## Purpose

- **Consistency**: Provide a uniform structure for agent definitions
- **Readability**: Enable human-readable agent configurations
- **Extensibility**: Allow easy addition of new agents without code changes
- **Documentation**: Self-documenting agent behavior and strategy
- **Validation**: Enable automated validation of agent configurations

## Document Structure

An agent configuration document MUST contain the following sections in order:

### 1. Front Matter (Required)

YAML front matter enclosed in `---` delimiters containing metadata:

```yaml
---
agent_id: "unique_agent_identifier"
agent_name: "Human Readable Agent Name"
version: "1.0.0"
agent_type: "analyst|investor|manager"
enabled: true
priority: 1
model_config:
  provider: "openai|anthropic|groq|deepseek|ollama"
  model: "model-name"
  temperature: 0.7
  max_tokens: 2000
tags:
  - "tag1"
  - "tag2"
created: "YYYY-MM-DD"
updated: "YYYY-MM-DD"
---
```

#### Front Matter Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `agent_id` | string | Yes | Unique identifier (lowercase, underscores) |
| `agent_name` | string | Yes | Human-readable name |
| `version` | string | Yes | Semantic version (MAJOR.MINOR.PATCH) |
| `agent_type` | enum | Yes | One of: `analyst`, `investor`, `manager` |
| `enabled` | boolean | Yes | Whether agent is active |
| `priority` | integer | No | Execution priority (1-10, default: 5) |
| `model_config` | object | Conditional | Required if agent uses LLM |
| `tags` | array | No | Categorization tags |
| `created` | date | Yes | Creation date (ISO 8601) |
| `updated` | date | Yes | Last update date (ISO 8601) |

### 2. Overview Section (Required)

Brief description of the agent's purpose and investment philosophy.

```markdown
## Overview

[2-3 sentence description of the agent's purpose and strategy]
```

### 3. Strategy Section (Required)

Detailed explanation of the investment strategy and methodology.

```markdown
## Strategy

### Investment Philosophy

[Detailed description of the core investment philosophy]

### Key Principles

1. [Principle 1]
2. [Principle 2]
3. [Principle 3]
...

### Decision Criteria

- **Primary Factors**: [List of primary decision factors]
- **Secondary Factors**: [List of secondary considerations]
- **Risk Considerations**: [How risk is evaluated]
```

### 4. Analysis Criteria Section (Required)

Specific metrics, thresholds, and analysis methods used by the agent.

```markdown
## Analysis Criteria

### Metrics

#### [Category Name] (Weight: X%)

| Metric | Threshold | Signal | Points |
|--------|-----------|--------|--------|
| Metric Name | > X or < Y | bullish/bearish | +/-N |
| ... | ... | ... | ... |

[Repeat for each category]

### Scoring System

- **Total Possible Score**: [Max points]
- **Bullish Threshold**: [Points needed for bullish signal]
- **Bearish Threshold**: [Points needed for bearish signal]
- **Neutral Range**: [Points range for neutral signal]

### Confidence Calculation

[Formula or method for calculating confidence level]
```

### 5. Implementation Section (Required)

Technical details about how the agent operates.

```markdown
## Implementation

### Data Requirements

- **Financial Metrics**: [List of required metrics]
- **Market Data**: [Price data, volume, etc.]
- **Alternative Data**: [News, sentiment, insider trades, etc.]
- **Time Period**: [Historical data needed]

### Processing Steps

1. **Data Collection**: [How data is gathered]
2. **Analysis**: [How analysis is performed]
3. **Signal Generation**: [How signals are determined]
4. **Output Format**: [Structure of the output]

### Dependencies

- API Requirements: [Required API endpoints]
- External Services: [Third-party services needed]
- Other Agents: [Dependencies on other agents]

### Rate Limits

- API Calls per Ticker: [Number]
- Total API Calls: [Number]
- Execution Time Estimate: [Seconds per ticker]
```

### 6. Examples Section (Required)

Sample scenarios demonstrating agent behavior.

```markdown
## Examples

### Example 1: [Scenario Name]

**Input:**
- Ticker: [SYMBOL]
- Date: [YYYY-MM-DD]
- Key Metrics: [Relevant metrics]

**Analysis:**
[Step-by-step analysis]

**Output:**
```json
{
  "signal": "bullish|bearish|neutral",
  "confidence": 85,
  "reasoning": {
    "category1": { ... },
    "category2": { ... }
  }
}
```

[Repeat for 2-3 examples]
```

### 7. Configuration Section (Optional)

Tunable parameters for agent customization.

```markdown
## Configuration

### Parameters

| Parameter | Type | Default | Range | Description |
|-----------|------|---------|-------|-------------|
| param_name | type | value | min-max | Description |
| ... | ... | ... | ... | ... |

### Environment Variables

- `ENV_VAR_NAME`: [Description]

### Feature Flags

- `feature_name`: [Description] (default: true/false)
```

### 8. References Section (Optional)

Citations and resources.

```markdown
## References

### Books

- [Book Title] by [Author] ([Year])

### Papers

- [Paper Title] ([Year]) - [Link]

### Articles

- [Article Title] - [Link]

### Related Agents

- [Agent Name] - [Relationship/reason]
```

### 9. Changelog Section (Optional)

Version history.

```markdown
## Changelog

### [1.0.0] - YYYY-MM-DD
#### Added
- Initial release

### [0.9.0] - YYYY-MM-DD
#### Changed
- [Description of changes]
```

## Agent Types

### Analyst Agents

- Focus on specific analysis dimensions (technical, fundamental, sentiment, valuation)
- Typically use rule-based logic or simple calculations
- Fast execution, no LLM required
- Output: Standardized signal with reasoning

**Examples**: `fundamentals_analyst`, `technicals_analyst`, `sentiment_analyst`

### Investor Agents

- Emulate famous investors' strategies and philosophies
- Use LLMs for nuanced reasoning and context understanding
- Slower execution due to LLM calls
- Output: Signal with detailed qualitative reasoning

**Examples**: `warren_buffett`, `peter_lynch`, `cathie_wood`

### Manager Agents

- Aggregate signals from multiple agents
- Make portfolio-level decisions
- Handle risk management and position sizing
- Output: Trading orders and portfolio recommendations

**Examples**: `risk_manager`, `portfolio_manager`

## Signal Format

All agents MUST output signals in this standardized JSON format:

```json
{
  "TICKER": {
    "signal": "bullish|bearish|neutral",
    "confidence": 0-100,
    "reasoning": {
      "category1": {
        "signal": "bullish|bearish|neutral",
        "confidence": 0-100,
        "metrics": { ... },
        "details": "explanation"
      },
      "category2": { ... }
    }
  }
}
```

### Signal Values

- `bullish`: Positive outlook, recommend buying or holding
- `bearish`: Negative outlook, recommend selling or avoiding
- `neutral`: Mixed or insufficient signals, no strong recommendation

### Confidence Scale

- **90-100**: Very High - Strong conviction with clear supporting evidence
- **70-89**: High - Solid evidence with minor concerns
- **50-69**: Moderate - Mixed signals, some uncertainty
- **30-49**: Low - Weak signals, significant uncertainty
- **0-29**: Very Low - Highly uncertain or conflicting data

## Validation Rules

1. **Front Matter**: Must be valid YAML and contain all required fields
2. **Sections**: All required sections must be present
3. **Agent ID**: Must match pattern `^[a-z][a-z0-9_]*$`
4. **Version**: Must follow semantic versioning
5. **Agent Type**: Must be one of the defined types
6. **Model Config**: Must specify valid provider and model if using LLM
7. **Metrics Table**: Must include columns for Metric, Threshold, Signal, Points
8. **Examples**: Must include at least one complete example
9. **JSON Outputs**: Must be valid JSON and follow the signal format

## File Naming Convention

Agent configuration files MUST follow this naming pattern:

```
{agent_id}.agent.md
```

Examples:
- `warren_buffett.agent.md`
- `fundamentals_analyst.agent.md`
- `risk_manager.agent.md`

## Best Practices

### Do's ✅

- Use clear, descriptive agent names
- Provide detailed reasoning for all criteria
- Include concrete examples with real-world metrics
- Document all thresholds and their rationale
- Update the version and updated date when making changes
- Keep strategies focused and coherent
- Use consistent terminology throughout

### Don'ts ❌

- Don't create overlapping agents with identical strategies
- Don't use arbitrary thresholds without justification
- Don't omit required sections
- Don't hardcode ticker-specific logic
- Don't create circular dependencies between agents
- Don't use non-standard signal values
- Don't include sensitive credentials or API keys

## Migration Path

To convert existing Python agents to Markdown format:

1. Extract metadata → Front Matter
2. Document strategy from code comments and logic → Strategy Section
3. List all metrics and thresholds → Analysis Criteria
4. Document API calls and dependencies → Implementation
5. Create test cases → Examples
6. Add configuration parameters → Configuration
7. Validate against this specification

## Extensibility

This specification supports extension through:

- **Custom Tags**: Add domain-specific tags in front matter
- **Additional Sections**: Append custom sections after required sections
- **Custom Fields**: Add extra fields to front matter (prefixed with `x_`)
- **Metadata**: Embed metadata in code blocks using data attributes

## Versioning

This specification follows semantic versioning:

- **MAJOR**: Incompatible changes requiring agent updates
- **MINOR**: Backward-compatible additions
- **PATCH**: Clarifications and bug fixes

Current Version: **1.0.0**

## Support

For questions, issues, or suggestions regarding this specification:

- Open an issue on GitHub
- Refer to the [Agent Guide](AGENT_GUIDE.md) for practical examples
- Review example agents in the `examples/` directory

---

**Last Updated**: 2024-10-18
**Specification Version**: 1.0.0
**Status**: Stable
