# ansible-nerd-fonts

[![CICD](https://github.com/jahrik/ansible-nerd-fonts/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-nerd-fonts/actions/workflows/cicd.yml)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-jahrik.nerd__fonts-blue?logo=ansible)](https://galaxy.ansible.com/ui/standalone/roles/jahrik/nerd_fonts/)

Installs [Nerd Fonts](https://github.com/ryanoasis/nerd-fonts) for use by terminal emulators, editors, and shell prompts. Installs [DejaVu Sans Mono](https://github.com/ryanoasis/nerd-fonts/tree/master/patched-fonts/DejaVuSansMono) and [JetBrains Mono](https://github.com/ryanoasis/nerd-fonts/tree/master/patched-fonts/JetBrainsMono) by default. Intended as a shared dependency for roles that reference Nerd Font glyphs (e.g. `jahrik.nvim`, `jahrik.zsh`, `jahrik.ghostty`).

## OS Support

| Platform | Install method |
|---|---|
| Arch Linux | `unarchive` from GitHub releases → `~/.local/share/fonts/` |
| Ubuntu / Debian | `unarchive` from GitHub releases → `~/.local/share/fonts/` |
| macOS | Homebrew Cask (`font-*-nerd-font`) |
| Steam Deck / SteamOS | `unarchive` from GitHub releases → `~/.local/share/fonts/` (no `sudo`) |

Steam Deck is detected automatically via `/etc/steamos-release`. All tasks run without `become` since SteamOS has a read-only root filesystem.

## Role Variables

```yaml
nerd_fonts_version: "3.3.0"   # Nerd Fonts release tag

nerd_fonts:
  - name: DejaVuSansMono
    creates: DejaVuSansMNerdFontMono-Regular.ttf
  - name: JetBrainsMono
    creates: JetBrainsMonoNerdFontMono-Regular.ttf
```

Add or remove entries from `nerd_fonts` to control which families are installed. The `creates` value is the sentinel file used for idempotency — set it to any TTF file produced by the zip that is unlikely to already exist.

Set `install: false` to remove installed font files.

## Task Flow

`tasks/main.yml` → `install.yml` (default) or `uninstall.yml` (when `install: false`)

**install.yml:**
1. Detect SteamOS via `/etc/steamos-release`.
2. Dispatch to `darwin.yml` (macOS) or `linux.yml` (all others).

**linux.yml:**
1. Install `fontconfig` via distro package manager (Arch: pacman, Debian: apt). Also installs `unzip` on Debian.
2. Create `~/.local/share/fonts`.
3. Download and extract each font zip from `github.com/ryanoasis/nerd-fonts/releases`, guarded by `creates:` for idempotency.
4. Notify `Fc-cache` handler on change.

**darwin.yml:**
- Installs `font-dejavu-sans-mono-nerd-font` and `font-jetbrains-mono-nerd-font` via `community.general.homebrew_cask` (`become: false`).

**uninstall.yml:**
- Removes each font's sentinel TTF file from `~/.local/share/fonts/`.

## Handlers

| Handler | Action |
|---|---|
| `Fc-cache` | `fc-cache -f ~/.local/share/fonts/` (non-privileged, never fails) |

## Testing

```bash
yamllint .
ansible-lint
molecule test                  # Ubuntu + Arch Linux (Docker)
molecule test -s steamdeck     # SteamOS simulation (Docker)
molecule test -s localhost     # Local machine
```

## Example Playbook

```yaml
- hosts: local
  roles:
    - { role: jahrik.nerd_fonts }
```

## Declaring as a Dependency

In another role's `meta/main.yml`:

```yaml
dependencies:
  - role: jahrik.nerd_fonts
```

## License

GPLv2

## Author Information

jahrik@gmail.com
