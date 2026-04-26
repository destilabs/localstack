# localstack

Working configurations for running open-source coding agents against open-weights or low-cost model backends. Use this when Claude Code rate-limits you, or when you want a CLI that is not vendor-locked.

## Tools

| Tool | License | Governance | Repository |
|---|---|---|---|
| Goose | Apache 2.0 | Linux Foundation AAIF | https://github.com/aaif-goose/goose |
| OpenCode | MIT | sst / Anomaly | https://github.com/sst/opencode |

Both are terminal-first agentic coding CLIs. Both speak the Model Context Protocol. Both let you swap the model behind the agent.

Install instructions: [install/goose.md](install/goose.md), [install/opencode.md](install/opencode.md).

## Models

| Model | Cost | Provider | Use when |
|---|---|---|---|
| DeepSeek V4 | $0.14 - $3.48 / M tokens | DeepSeek API | You want the cheapest cloud option for hard refactors |
| Qwen3-Coder | Free tier (20 req/min, 200/day) | OpenRouter | Everyday agentic coding |
| Kimi K2 Thinking | Free (40 req/min) | NVIDIA NIM | Free fallback with extended reasoning |
| Gemma 4 | Free | Ollama (local) | Offline, no key, no rate limit |

Configuration recipes: [models/goose.md](models/goose.md), [models/opencode.md](models/opencode.md).

## Cost comparison

The baseline $18,124 figure was computed by running [`claude-cost`](https://github.com/destilabs/claude-cost) over a real 30-day window of Claude Code usage (Mar 27 - Apr 26, 2026). The dataset covered 57,123 assistant turns across 212 sessions in 23 repositories, with cache hit rates between 75% and 99% across sessions.

Cost equivalents for the alternative models are computed by scaling against Claude Opus 4.7's output rate of $75 per million tokens. Output tokens dominate total spend in coding workloads, so the output-rate ratio is a reasonable first-order estimate. Example: DeepSeek V4-Pro at $3.48 per million output is 4.6% of Opus's rate, so the same workload would cost approximately $18,124 x 0.046 = $834.

To replicate the methodology on your own usage, install `claude-cost` and run it against your `~/.claude/projects/` directory:

```bash
uv run --with claude-cost claude-cost
```

Then multiply your reported total by each model's output-rate ratio in the table below.

| Model | Provider | Input ($/M) | Output ($/M) | Est. 30-day cost | Notes |
|---|---|---|---|---|---|
| Claude Opus 4.7 | Anthropic API | $15 | $75 | **$18,124** | Baseline; 75-99% cache hit rate |
| DeepSeek V4-Pro | DeepSeek API | $1.74 | $3.48 | ~$834 | Auto-cached; closest performance to Opus |
| Qwen3-Coder 480B | OpenRouter | $0.22 | $1.80 | ~$435 | Free tier 20 req/min, 200/day |
| Kimi K2 Thinking | NVIDIA NIM (free) | $0 | $0 | $0 | 40 req/min cap |
| Kimi K2 Thinking | Moonshot AI | $0.55 | $2.20 | ~$525 | Pay-as-you-go alternative to NIM |
| Gemma 4 31B | GCP Vertex AI | $0.14 | $0.40 | ~$96 | Pay-as-you-go, no rate limit |
| Gemma 4 e4b / 26b | Local (Ollama) | $0 | $0 | ~$0 | Electricity only; quality drop on hard refactors |

### Notes

- Estimates assume the alternative matches Anthropic's caching efficiency. If caching is weaker or absent, real cost is typically 2-5x higher than shown.
- Free-tier figures ($0) assume your throughput stays within the rate limit. For heavy days like a 4,440-turn session in the source dataset, free-tier providers would have hit their daily cap within an hour.
- Gemma 4 local at $0 is real but the model is meaningfully weaker on agentic coding than DeepSeek V4-Pro or Kimi K2 Thinking. The 26b/31b sizes need 32 GB+ RAM. On 16 GB Macs only `gemma4:e4b` runs reliably, and it is not a substitute for frontier-class models on hard refactors.
- The 90/10 output-to-input ratio assumed here is typical for coding agents but not universal. Workloads with heavy context loading (large codebases, RAG pipelines) will skew the ratio toward input and shift the relative cost rankings.
- Anthropic Sonnet 4.6 is not in this table. For reference, switching the same workload to Sonnet (output rate $15 per million, 0.20x ratio) would have cost approximately $3,625 over the same window.

## Hardware notes for local Gemma 4

| Mac unified RAM | Recommended Gemma 4 size | Disk |
|---|---|---|
| 16 GB | `gemma4:e4b` | 9.6 GB |
| 32 GB | `gemma4:26b` | 18 GB |
| 48 GB+ | `gemma4:31b` | 20 GB |

Smaller machines will swap on tight RAM. Larger sizes need workstation-class hardware.

## Verified versions (April 26, 2026)

- Goose v1.32.0
- OpenCode v1.14.25
- DeepSeek V4 (preview release April 24, 2026)
- Qwen3-Coder family on OpenRouter
- Kimi K2 Thinking on NVIDIA NIM
- Gemma 4 (released April 7, 2026)

## Caveats

Benchmark numbers cited online for these models (SWE-Bench Verified, LiveCodeBench, etc.) come from secondary sources or vendor-published claims. Independent verification is sparse as of April 2026. Run your own evaluation on your own task before relying on a model in production.

DeepSeek V4 is a preview release. The Anthropic-compatible endpoint mapping may change at GA.

OpenCode cannot use Claude Pro / Max OAuth tokens since January 2026. API keys still work.
