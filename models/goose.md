# Models in Goose

This file shows how to configure each of the four model backends in Goose.

The cleanest path is `goose configure` for built-in providers (Anthropic, OpenAI, Google, OpenRouter, Ollama). For custom OpenAI-compatible endpoints (NVIDIA NIM, DeepSeek if not in the menu), drop a JSON file in `~/.config/goose/custom_providers/`.

API keys go to the macOS keychain via `goose configure`. They are not stored in `~/.config/goose/config.yaml`.

## DeepSeek V4

Get a key at https://platform.deepseek.com/api_keys.

DeepSeek exposes both an OpenAI-compatible endpoint (`/v1`) and an Anthropic-compatible endpoint (`/anthropic`). Goose uses the OpenAI-compatible one.

If DeepSeek appears in the `goose configure` menu:

```bash
goose configure
# Pick: Configure Providers -> DeepSeek
# Model: deepseek-v4-pro (high quality) or deepseek-v4-flash (cheap)
```

If it does not, define a custom provider:

```bash
mkdir -p ~/.config/goose/custom_providers
cat > ~/.config/goose/custom_providers/deepseek.json <<'EOF'
{
  "name": "deepseek",
  "engine": "openai",
  "api_key_env": "DEEPSEEK_API_KEY",
  "base_url": "https://api.deepseek.com/v1/chat/completions"
}
EOF
export DEEPSEEK_API_KEY=sk-...
```

Then run `goose configure`, pick the custom provider, and set the model to `deepseek-v4-pro` or `deepseek-v4-flash`.

Pricing (April 26, 2026):

| Model | Input | Output |
|---|---|---|
| deepseek-v4-flash | $0.14 / M tokens | $0.28 / M tokens |
| deepseek-v4-pro | $1.74 / M tokens | $3.48 / M tokens |

## Qwen3-Coder via OpenRouter

Get a key at https://openrouter.ai/keys.

```bash
goose configure
# Pick: OpenRouter Login (OAuth) or Manual Configuration
# Model: qwen/qwen3-coder
```

After this, `~/.config/goose/config.yaml` should contain:

```yaml
GOOSE_PROVIDER: openrouter
GOOSE_MODEL: qwen/qwen3-coder
OPENROUTER_HOST: https://openrouter.ai
```

Free tier limits on OpenRouter: 20 requests per minute, 200 per day per model. Above that, OpenRouter routes to its paid pool at the model's pass-through rate.

## Kimi K2 Thinking via NVIDIA NIM

Get a key at https://build.nvidia.com/settings/api-keys. The full 56-character key matters - partial keys return 401.

NIM is OpenAI-compatible. Define it as a custom provider:

```bash
mkdir -p ~/.config/goose/custom_providers
cat > ~/.config/goose/custom_providers/nvidia-nim.json <<'EOF'
{
  "name": "nvidia-nim",
  "engine": "openai",
  "api_key_env": "NVIDIA_API_KEY",
  "base_url": "https://integrate.api.nvidia.com/v1/chat/completions"
}
EOF
export NVIDIA_API_KEY=nvapi-...
```

Then:

```bash
goose configure
# Pick: Custom Provider -> nvidia-nim
# Model: moonshotai/kimi-k2-thinking
```

Available models on NIM include:

- `moonshotai/kimi-k2-thinking` (extended reasoning)
- `mistralai/devstral-2-123b-instruct-2512` (everyday coding)
- `moonshotai/kimi-k2.5` (fast tier)

Free tier: 40 requests per minute. Reasoning models need long timeouts (180s+). If responses cut off, raise the timeout in your shell before launching Goose.

## Gemma 4 local via Ollama

Install Ollama:

```bash
brew install ollama
ollama serve
```

Leave `ollama serve` running in a separate terminal, or use the macOS .app to keep it alive in the background.

Pull the model. Pick the size by RAM:

| Mac unified RAM | Pull command | Disk |
|---|---|---|
| 16 GB | `ollama pull gemma4:e4b` | 9.6 GB |
| 32 GB | `ollama pull gemma4:26b` | 18 GB |
| 48 GB+ | `ollama pull gemma4:31b` | 20 GB |

Configure Goose:

```bash
goose configure
# Pick: Ollama
# Host: http://localhost:11434
# Model: gemma4:e4b (or whichever you pulled)
```

Your `config.yaml` should now contain:

```yaml
GOOSE_PROVIDER: ollama
GOOSE_MODEL: gemma4:e4b
OLLAMA_HOST: http://localhost:11434
```

No API key needed.

To keep Ollama warm so first-token latency stays low:

```bash
OLLAMA_KEEP_ALIVE=24h ollama serve
```

## Switching providers

Inside a session: `/model` lists available models from configured providers and lets you pick.

From the shell: edit `GOOSE_PROVIDER` and `GOOSE_MODEL` in `~/.config/goose/config.yaml`, then start a new session with `goose session`.

## Notes on uncertainty

The exact provider menu in `goose configure` changes between versions. If a provider above is not in your interactive menu, fall back to the `custom_providers` JSON pattern.

Some Goose versions read API keys from environment variables first, then keychain. If a provider does not pick up your key, export the relevant env var (`DEEPSEEK_API_KEY`, `NVIDIA_API_KEY`, `OPENROUTER_API_KEY`) before running `goose session`.

The `base_url` format for custom providers - whether to include `/chat/completions` or stop at `/v1` - varies by Goose version. The pattern above matches the official docs example. If your provider returns 404, try the shorter form.
