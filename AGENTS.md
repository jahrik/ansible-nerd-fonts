# ansible-nerd-fonts

Installs Nerd Fonts (DejaVu Sans Mono, JetBrains Mono) across Arch Linux, Debian/Ubuntu, macOS, and SteamOS (Steam Deck). Shared dependency for terminal/editor roles.

## Variables

| Variable | Default | Description |
|---|---|---|
| `nerd_fonts_version` | `3.3.0` | GitHub release tag |
| `nerd_fonts` | see defaults | `{name, creates}` font list |
| `install` | `true` | Set `false` to uninstall |

## Testing

```bash
uv sync
source .venv/bin/activate
yamllint .
ansible-lint
molecule test
```

### Examples

```bash
# Target specific environments
molecule test -s steamdeck
molecule converge -s localhost
molecule destroy
```

## Architecture

- **SteamOS**: Auto-detected via `/etc/steamos-release`. Runs without `become`.
- **macOS**: Uses `community.general.homebrew_cask`.
- **Linux**: Downloads zips to `~/.local/share/fonts/`, triggers `Fc-cache` handler (`fc-cache -f ~/.local/share/fonts/`). Idempotent via `creates` sentinel files.
- **CI**: Runs via GitHub Actions using Ubuntu, Arch (Docker), and macOS local execution.
