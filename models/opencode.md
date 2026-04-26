# Models in OpenCode

This file shows how to configure each of the four model backends in OpenCode.

OpenCode reads `~/.config/opencode/opencode.json` (global) and `opencode.json` at the project root (per-project override). Both accept JSON or JSONC.

For built-in providers (Anthropic, OpenAI, OpenRouter, DeepSeek, Ollama, etc.), `opencode auth login` is the fastest path. For custom OpenAI-compatible endpoints (NVIDIA NIM), edit `opencode.json` directly.

## DeepSeek V4

Get a key at https://platform.deepseek.com/api_keys.

```bash
opencode auth login
# Pick: DeepSeek
# Paste the key
```

Then in `~/.config/opencode/opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "deepseek/deepseek-v4-pro"
}
```

Available models: `deepseek-v4-pro` (high quality), `deepseek-v4-flash` (cheap).

If DeepSeek is not in the auth menu of your OpenCode version, configure it as a custom OpenAI-compatible provider:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "deepseek": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "DeepSeek",
      "options": {
        "baseURL": "https://api.deepseek.com/v1",
        "apiKey": "{env:DEEPSEEK_API_KEY}"
      },
      "models": {
        "deepseek-v4-pro": { "name": "DeepSeek V4 Pro" },
        "deepseek-v4-flash": { "name": "DeepSeek V4 Flash" }
      }
    }
  },
  "model": "deepseek/deepseek-v4-pro"
}
```

```bash
export DEEPSEEK_API_KEY=sk-...
```

## Qwen3-Coder via OpenRouter

```bash
opencode auth login
# Pick: OpenRouter
# Paste the key from https://openrouter.ai/keys
```

Then:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "openrouter/qwen/qwen3-coder"
}
```

Free tier limits on OpenRouter: 20 requests per minute, 200 per day per model.

## Kimi K2 Thinking via NVIDIA NIM

NIM is OpenAI-compatible. There is no built-in OpenCode provider entry, so configure it as a custom provider:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "nvidia-nim": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "NVIDIA NIM",
      "options": {
        "baseURL": "https://integrate.api.nvidia.com/v1",
        "apiKey": "{env:NVIDIA_API_KEY}",
        "timeout": 180000
      },
      "models": {
        "moonshotai/kimi-k2-thinking": { "name": "Kimi K2 Thinking" },
        "mistralai/devstral-2-123b-instruct-2512": { "name": "Devstral 2 123B" }
      }
    }
  },
  "model": "nvidia-nim/moonshotai/kimi-k2-thinking"
}
```

Get a key at https://build.nvidia.com/settings/api-keys.

```bash
export NVIDIA_API_KEY=nvapi-...
```

Free tier: 40 requests per minute.

The `timeout: 180000` (180 seconds) is required for reasoning models like Kimi K2 Thinking. Without it, long reasoning chains time out before the model responds.

## Gemma 4 local via Ollama

Install Ollama:

```bash
brew install ollama
ollama serve
```

Pull the model. Pick the size by RAM:

| Mac unified RAM | Pull command | Disk |
|---|---|---|
| 16 GB | `ollama pull gemma4:e4b` | 9.6 GB |
| 32 GB | `ollama pull gemma4:26b` | 18 GB |
| 48 GB+ | `ollama pull gemma4:31b` | 20 GB |

Configure OpenCode:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (local)",
      "options": {
        "baseURL": "http://localhost:11434/v1"
      },
      "models": {
        "gemma4:e4b": { "name": "Gemma 4 E4B" },
        "gemma4:26b": { "name": "Gemma 4 26B" },
        "gemma4:31b": { "name": "Gemma 4 31B" }
      }
    }
  },
  "model": "ollama/gemma4:e4b"
}
```

No API key needed.

To keep Ollama warm:

```bash
OLLAMA_KEEP_ALIVE=24h ollama serve
```

## Switching models

Inside OpenCode: `/model` lists configured models, pick to switch.

From the shell: edit the `model` field in `~/.config/opencode/opencode.json` (or per-project `opencode.json`) and restart the session.

## Per-project overrides

Drop an `opencode.json` at the root of any project to override the global model just for that repo:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "ollama/gemma4:e4b"
}
```

Useful when you want offline models for one project (no network egress) and cloud models elsewhere.
