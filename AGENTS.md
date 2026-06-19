# ansible-nerd-fonts

Ansible role that installs Nerd Fonts (DejaVu Sans Mono, JetBrains Mono) across
Arch Linux, Debian/Ubuntu, macOS, and SteamOS (Steam Deck).

## Key Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `nerd_fonts_version` | `3.3.0` | Nerd Fonts release tag to download |
| `nerd_fonts` | see defaults | List of `{name, creates}` font families to install |
| `install` | `true` | Set to `false` to uninstall font files |

## Task Flow

`tasks/main.yml` → `install.yml` (always) and `uninstall.yml` (when `install: false`)

**install.yml:**
1. Detect SteamOS via `/etc/steamos-release`.
2. Dispatch to `darwin.yml` (macOS) or `linux.yml` (all others).

**linux.yml:**
1. Install `fontconfig` (and `unzip` on Debian) via package manager.
2. Create `~/.local/share/fonts`.
3. Download each font zip from GitHub releases via `unarchive`, notify `Fc-cache`.
4. SteamOS: all tasks run without `become` (read-only root).

**darwin.yml:**
- Installs `font-dejavu-sans-mono-nerd-font` and `font-jetbrains-mono-nerd-font` via Homebrew Cask.

## Testing

```bash
uv run yamllint .
uv run ansible-lint
uv run molecule test -s localhost
uv run molecule test
```

Run with Podman locally:
```bash
mtest test
mtest test -s localhost
```
