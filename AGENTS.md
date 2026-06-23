# ansible-nerd-fonts

Ansible role that installs Nerd Fonts (DejaVu Sans Mono, JetBrains Mono) across
Arch Linux, Debian/Ubuntu, macOS, and SteamOS (Steam Deck). Shared dependency
for `jahrik.nvim`, `jahrik.zsh`, and `jahrik.ghostty`.

## Key Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `nerd_fonts_version` | `3.3.0` | Nerd Fonts release tag to download |
| `nerd_fonts` | see defaults | List of `{name, creates}` font families to install |
| `install` | `true` | Set to `false` to remove installed font files |

## Task Flow

`tasks/main.yml` → `install.yml` (always) or `uninstall.yml` (when `install: false`)

**install.yml:**
1. Detect SteamOS via `/etc/steamos-release`.
2. Dispatch to `darwin.yml` (macOS) or `linux.yml` (all others).

**linux.yml:**
1. Install `fontconfig` (+ `unzip` on Debian) via package manager.
2. Create `~/.local/share/fonts`.
3. Download each font zip from GitHub releases via `unarchive`, guarded by `creates:`.
4. Notify `Fc-cache` handler on change.
5. SteamOS: all tasks run without `become` (read-only root).

**darwin.yml:**
- Installs fonts via `community.general.homebrew_cask` (`become: false`).

**uninstall.yml:**
- Removes each font's sentinel TTF file on Linux.

## Molecule Scenarios

| Scenario | Platforms | Purpose |
|----------|-----------|---------|
| `default` | Ubuntu 24.04 + Arch Linux (Docker) | Standard Linux install |
| `localhost` | Local machine | macOS CI + local dev |
| `steamdeck` | Arch Linux with `/etc/steamos-release` stub (Docker) | SteamOS path |

## Testing

```bash
uv sync
source .venv/bin/activate
yamllint .
ansible-lint
molecule test
```

Localhost scenario (used in CI on the macOS GitHub Actions runner):

```bash
molecule converge -s localhost
molecule verify -s localhost
```

## CI

- **Lint**: yamllint + ansible-lint
- **Molecule**: Ubuntu 24.04 + Arch Linux via Docker (`molecule/default`)
- **Steam Deck**: Arch container with `/etc/steamos-release` stubbed (`molecule/steamdeck`)
- **macOS**: `ansible-playbook` against `macos-latest` GHA runner (`molecule/localhost`)
- **Release**: publishes to Ansible Galaxy on merge to `main`
