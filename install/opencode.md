# Install OpenCode

OpenCode v1.14.25 (April 25, 2026) is the version this repo targets.

- Repository: https://github.com/sst/opencode
- License: MIT
- Maintainer: sst / Anomaly

## macOS / Linux

```bash
curl -fsSL https://opencode.ai/install | bash
```

Alternative install methods:

```bash
npm install -g opencode-ai
brew install sst/tap/opencode
```

Windows users have Scoop and PowerShell installers documented in the official docs.

Verify:

```bash
opencode --version
```

## First-run authentication

```bash
opencode auth login
```

The interactive flow lists supported providers (Anthropic, OpenAI, Google, OpenRouter, DeepSeek, Groq, Ollama, and others). Pick one and paste a key. Credentials are stored in `~/.local/share/opencode/auth.json`.

Note: as of January 2026, OpenCode cannot use Claude Pro or Max OAuth subscription tokens. An Anthropic API key still works.

## Config file

Global config: `~/.config/opencode/opencode.json`. Per-project override: `opencode.json` at the repo root.

A minimal config:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-5"
}
```

The `$schema` line gives autocomplete in editors that read JSON Schema (VS Code, JetBrains, etc.). Both files accept JSON or JSONC (JSON with comments).

## Verify

```bash
opencode
```

Starts the TUI. Confirm the model responds, then `Ctrl+C` to exit.

## Where things live

| Path | Purpose |
|---|---|
| `~/.config/opencode/opencode.json` | Global config (provider, model, options) |
| `<project>/opencode.json` | Per-project overrides |
| `~/.local/share/opencode/auth.json` | Stored credentials |

## Updating

```bash
opencode upgrade
```

Or re-run the install command.

## Uninstall

```bash
rm "$(which opencode)"
rm -rf ~/.config/opencode ~/.local/share/opencode
```
