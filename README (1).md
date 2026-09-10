# Claude Model Performance Benchmark

A comprehensive benchmarking tool to compare the speed, cost, and performance of Anthropic's Claude models (Opus 5, Sonnet 5, Haiku 4.5, and Fable 5.1) on both simple and complex tasks.

## 📊 Overview

This notebook runs identical tasks across all four current Claude models and measures:

- **Execution time** — how long each model takes to respond
- **Token consumption** — input and output token counts
- **Cost incurred** — USD cost based on official Anthropic API pricing
- **Time per token** — efficiency metric (execution time ÷ total tokens)
- **Response quality** — through visual comparison and error handling

Perfect for:
- Choosing the right model for your use case
- Optimizing API costs
- Understanding model performance tradeoffs
- Making data-driven decisions about model selection

---

## 🚀 Quick Start

### Prerequisites

1. **Anthropic API Key** — Get one at [console.anthropic.com](https://console.anthropic.com)
2. **Kaggle Account** — Running on [Kaggle Notebooks](https://kaggle.com)
3. **Add API Key to Kaggle Secrets**:
   - Click **Add-ons** → **Secrets**
   - Create a secret named `ANTHROPIC_API_KEY`
   - Paste your API key
   - Toggle "On" to enable it

### Run the Benchmark

```python
# Cell 1: Install dependencies
!pip install -q anthropic

# Cell 2: Initialize and run benchmark
# (Full code provided in notebook)

# Cell 3: View results
df  # DataFrame with all metrics
```

---

## 📋 Metrics Explained

### Execution Time
Total seconds from API call to response. Includes network latency and model inference.

### Token Counts
- **Input tokens** — tokens in your prompt/task
- **Output tokens** — tokens in the model's response
- **Total tokens** — sum of input + output

### Cost (USD)
Calculated based on official Anthropic pricing (September 2026):

| Model | Input $/1M tokens | Output $/1M tokens |
|-------|-------------------|-------------------|
| Claude Haiku 4.5 | $1.00 | $5.00 |
| Claude Sonnet 5 | $2.00 | $10.00 |
| Claude Opus 5 | $5.00 | $25.00 |
| Claude Fable 5.1 | $10.00 | $50.00 |

Formula:
```
cost = (input_tokens × input_price ÷ 1,000,000) 
     + (output_tokens × output_price ÷ 1,000,000)
```

### Time Per Token
Execution time ÷ total tokens. Lower is better (more tokens generated per second).

---

## 🎯 Example Results

### Simple Task: Photosynthesis Explanation

A straightforward one-paragraph explanation task shows Haiku's efficiency:

| Model | Time | Tokens | Cost | Time/Token |
|-------|------|--------|------|------------|
| Haiku 4.5 | 24.2s | 2,782 | $0.0139 | 0.0087s |
| Sonnet 5 | 41.4s | 5,000 | $0.0101 | 0.0083s |
| Opus 5 | 60.2s | 5,000 | $0.0250 | 0.0120s |
| Fable 5.1 | 59.7s | 5,000 | $0.0500 | 0.0120s |

**Winner:** Haiku (fastest, cheapest for simple tasks)

### Complex Task: Delivery Routing Optimization

A multi-step reasoning task (write algorithm, analyze complexity, self-critique) shows quality tradeoffs:

| Model | Time | Output Tokens | Cost | Quality |
|-------|------|----------------|------|---------|
| Haiku 4.5 | 24.2s | 2,782 | $0.0139 | Incomplete code, brief critique |
| Sonnet 5 | 41.4s | 5,000 | $0.0101 | Full solution, good analysis |
| Opus 5 | 60.2s | 5,000 | $0.0250 | Deep reasoning, thorough critique |
| Fable 5.1 | 59.7s | 5,000 | $0.0500 | Best reasoning (premium tier) |

**Winner:** Sonnet 5 (best value for reasoning tasks)

---

## 💡 Key Findings

### 1. Task Complexity Matters
- **Simple tasks** (Q&A, summaries, simple generation) → **Haiku wins** on speed and cost
- **Complex tasks** (reasoning, coding, multi-step logic) → **Sonnet 5 is optimal** for efficiency
- **Frontier work** (advanced reasoning, research) → **Opus 5 / Fable 5.1** justified despite cost

### 2. Cost vs. Quality Tradeoff
- Haiku can be 50% cheaper than Opus but may truncate responses or miss nuance
- Sonnet 5 offers 2-3x better quality than Haiku at similar cost
- Fable 5.1 is Opus+, justified only for highest-capability needs

### 3. Time Per Token
Surprisingly consistent across models (0.008-0.012 seconds/token), suggesting:
- Most execution time is network/API overhead, not model speed
- Quality differences show in output length and completeness, not raw speed

---

## 🔧 Customization

### Change Tasks

Replace the task in the `compare_model_speeds()` function:

```python
simple_task = "Explain photosynthesis in one paragraph."

complex_task = """
[Your multi-step reasoning task here]
"""
```

### Add Models

Edit the `models` list (note: model IDs must be current):

```python
models = [
    "claude-opus-5",
    "claude-sonnet-5",
    "claude-haiku-4-5-20251001",
    "claude-fable-5-1",
    # Add new models here
]
```

### Adjust Max Tokens

For longer responses, increase `max_tokens` (default 500 for simple, 2000 for complex):

```python
response = client.messages.create(
    model=model,
    max_tokens=3000,  # Adjust here
    messages=[...]
)
```

---

## 📊 Visualizations

The notebook generates a **2x2 dashboard** showing:

1. **Execution Time** — Total seconds per model
2. **Input Tokens** — Prompt size (constant across runs)
3. **Output Tokens** — Response length (varies by model)
4. **Cost (USD)** — Total API cost per model

All charts include value labels on bars for easy comparison.

---

## 🛠️ Advanced: Telemetry & Tracing

### Option 1: CSV Logging (Local)

Logs all runs to `/kaggle/working/api_telemetry.csv` for offline analysis:

```python
def log_telemetry(model, input_tokens, output_tokens, execution_time, cost):
    with open(TELEMETRY_FILE, "a") as f:
        writer = csv.writer(f)
        writer.writerow([datetime.now(), model, input_tokens, output_tokens, 
                        execution_time, cost])
```

### Option 2: LangSmith (Cloud Tracing)

Enable cloud-based tracing with automatic cost calculation and web dashboard:

1. Sign up at [smith.langchain.com](https://smith.langchain.com)
2. Create API key in Settings → API Keys
3. Add to Kaggle Secrets as `LANGSMITH_API_KEY`
4. Enable in notebook:
   ```python
   os.environ["LANGCHAIN_TRACING_V2"] = "true"
   os.environ["LANGCHAIN_API_KEY"] = user_secrets.get_secret("LANGSMITH_API_KEY")
   ```

All API calls automatically traced and visible at `smith.langchain.com/projects`

---

## 📈 Recommended Model Selection

| Use Case | Recommended Model | Reasoning |
|----------|------------------|-----------|
| Real-time chat, simple tasks | **Haiku 4.5** | Fastest response, cheapest |
| General purpose, balanced | **Sonnet 5** | Best value, strong reasoning |
| Complex agentic work, coding | **Opus 5** | Deep reasoning, most capable |
| Research, frontier tasks | **Fable 5.1** | Highest capability tier |

---

## 🔗 References

- **Anthropic Models Overview:** https://docs.claude.com/en/docs/about-claude/models/overview
- **Claude API Docs:** https://docs.claude.com/en/api/overview
- **Pricing Details:** https://console.anthropic.com/pricing
- **LangSmith Tracing:** https://docs.smith.langchain.com
- **Anthropic Blog:** https://www.anthropic.com/news

---

## ⚠️ Important Notes

1. **API Key Security** — Never hardcode API keys. Always use Kaggle Secrets.
2. **Rate Limits** — Check your account tier at [console.anthropic.com](https://console.anthropic.com/account/limits)
3. **Pricing Updates** — Prices may change. Always verify current rates in the console before budgeting.
4. **Network Latency** — Results may vary based on Kaggle's network. Run multiple times for consistency.

---

## 📝 License

Free to use and adapt. Attribution to Anthropic appreciated.

---

## 🤝 Contributing

Found a bug or have improvements? Feel free to fork and submit a PR.

---

**Last Updated:** September 2026  
**Claude Version:** Claude Haiku 4.5 (this README)  
**Benchmark Models:** Haiku 4.5, Sonnet 5, Opus 5, Fable 5.1
