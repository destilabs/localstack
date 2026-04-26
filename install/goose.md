# Install Goose

Goose v1.32.0 (April 23, 2026) is the version this repo targets.

- Repository: https://github.com/aaif-goose/goose
- License: Apache 2.0
- Governance: Linux Foundation, Agentic AI Foundation (AAIF)

## macOS / Linux

```bash
curl -fsSL https://github.com/aaif-goose/goose/releases/download/stable/download_cli.sh | bash
```

The installer drops the `goose` binary on your PATH. Verify:

```bash
goose --version
```

The desktop app for macOS, Linux, and Windows is available from https://goose-docs.ai/docs/getting-started/installation.

## First-run configuration

```bash
goose configure
```

The interactive flow asks you to pick a provider and a model. API keys are stored in the macOS keychain under the service `goose`, not in any config file.

Goose's config file lives at `~/.config/goose/config.yaml`. After `goose configure` completes, you should see lines like:

```yaml
GOOSE_PROVIDER: openrouter
GOOSE_MODEL: qwen/qwen3-coder
OPENROUTER_HOST: https://openrouter.ai
```

API keys do not appear in this file - they are in the keychain.

## Verify

```bash
goose session
```

Starts an interactive session. Type a prompt, confirm the model responds, then `/exit`.

## Where things live

| Path | Purpose |
|---|---|
| `~/.config/goose/config.yaml` | Provider selection, extensions, host overrides |
| `~/.config/goose/custom_providers/` | JSON files defining custom OpenAI-compatible providers |
| `~/.local/share/goose/sessions/sessions.db` | Session history (SQLite) |
| `~/.local/state/goose/logs` | Logs |
| macOS keychain (service `goose`) | API keys |

## Updating

Re-run the install command. The installer overwrites the existing binary.

## Uninstall

```bash
rm "$(which goose)"
rm -rf ~/.config/goose ~/.local/share/goose ~/.local/state/goose
```

API keys remain in the keychain unless you delete them via Keychain Access (search for `goose`).
