# ansible-nerd-fonts

[![CICD](https://github.com/jahrik/ansible-nerd-fonts/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-nerd-fonts/actions/workflows/cicd.yml)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-jahrik.nerd__fonts-blue?logo=ansible)](https://galaxy.ansible.com/ui/standalone/roles/jahrik/nerd_fonts/)

Installs [Nerd Fonts](https://github.com/ryanoasis/nerd-fonts). Installs DejaVu Sans Mono and JetBrains Mono by default. Useful as a shared dependency for roles that reference Nerd Font glyphs (e.g., `jahrik.nvim`, `jahrik.zsh`).

## Quickstart

```yaml
# Example Playbook
- hosts: local
  roles:
    - { role: jahrik.nerd_fonts }
```

```yaml
# Declaring as a dependency in meta/main.yml
dependencies:
  - role: jahrik.nerd_fonts
```

## Variables

```yaml
nerd_fonts_version: "3.3.0"   # Release tag

# Override to install different families. 'creates' is the sentinel file for idempotency.
nerd_fonts:
  - name: DejaVuSansMono
    creates: DejaVuSansMNerdFontMono-Regular.ttf
  - name: JetBrainsMono
    creates: JetBrainsMonoNerdFontMono-Regular.ttf
```

To remove fonts, set `install: false`.

## OS Support

| Platform | Method |
|---|---|
| Arch / Ubuntu / Debian | Download zip → extract to `~/.local/share/fonts/` |
| macOS | Homebrew Cask (`font-*-nerd-font`) |
| Steam Deck / SteamOS | Detected via `/etc/steamos-release` (extracted without `become`) |

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
# Test specific scenarios
molecule test -s steamdeck
molecule test -s localhost
```
