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
