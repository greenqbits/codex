# greenqbits fork notes (openai/codex -> greenqbits/codex)

This repository is a private fork of `openai/codex`.

Local clone:
- `/home/idurando/Projects/GreenQbits/Codex/Repo/codex`

Git remotes:
- `origin` (SSH push): `ssh://git@github.com/greenqbits/codex.git`
- `upstream` (fetch): `https://github.com/openai/codex.git`
- Upstream push is intentionally disabled (`upstream` push URL is a sentinel string).

## Upstream sync (merge-based)

Update local + push to `origin`:

```bash
git fetch upstream --prune
git fetch origin --prune

git checkout main
git merge --no-ff upstream/main

git push origin main
```

## Collaboration modes (Code + Plan)

User-facing surfaces (TUI/clients) are intentionally scoped to:
- Code (default)
- Plan

Other collaboration modes may still exist internally, but are not surfaced by the TUI.

## Plan mode effort (sub-agent effort) toggle

Goal:
- Plan mode defaults to `xhigh` reasoning effort.
- Plan effort is toggleable between `high` and `xhigh`.
- Sub-agent spawn effort follows the effective Plan reasoning effort.

Implementation:
- `codex-rs/core/src/models_manager/collaboration_mode_presets.rs`
  - Plan preset default uses `ReasoningEffort::XHigh`.
- `codex-rs/core/src/models_manager/manager.rs`
  - `list_collaboration_modes(&self, config: &Config)` seeds presets with:
    - global `config.model` / `config.model_reasoning_effort`
    - per-mode overrides from `config.collaboration_modes.*`
- `codex-rs/core/src/config/types.rs`
  - `CollaborationModesConfig` includes per-mode keys (notably `plan` and `code`).

## Config (hl1)

Enable collaboration modes feature:

```toml
[features]
collaboration_modes = true
```

Set Plan effort override (default xhigh; switch to high when needed):

```toml
[collaboration_modes.plan]
model_reasoning_effort = "xhigh"
```

Config file path:
- `/home/idurando/.codex/config.toml`

## Desktop integration (hl1 / XFCE)

Menu entry:
- `/home/idurando/.local/share/applications/greenqbits-codex.desktop` (Name: `Codex`)

Launcher wrapper:
- `/home/idurando/.local/bin/greenqbits-codex` (execs `~/.local/bin/codex`)

Desktop shortcut:
- `/home/idurando/Desktop/Codex.desktop`

Icon:
- `greenqbits-codex` (ChatGPT icon; installed under `~/.local/share/icons/hicolor/*/apps/greenqbits-codex.png` and `~/.local/share/icons/hicolor/scalable/apps/greenqbits-codex.svg`)

## Build (Arch Linux)

Build:

```bash
cd codex-rs
cargo build -p codex-cli --release
```

Install (local user):

```bash
install -Dm755 codex-rs/target/release/codex /home/idurando/.local/bin/codex
codex --version
```

## Verification (fast)

```bash
cd codex-rs
cargo check
cargo test -p codex-core list_collaboration_modes_ -- --nocapture
```
