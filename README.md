# ansible-nerd-fonts

[![CICD](https://github.com/jahrik/ansible-nerd-fonts/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-nerd-fonts/actions/workflows/cicd.yml)

Installs [Nerd Fonts](https://github.com/ryanoasis/nerd-fonts) for use by terminal emulators, editors, and prompts. Installs DejaVu Sans Mono and JetBrains Mono by default.

## OS Support

| Platform | Install method |
|---|---|
| Arch Linux | `unarchive` from GitHub releases |
| Ubuntu / Debian | `unarchive` from GitHub releases |
| macOS | Homebrew Cask |
| Steam Deck / SteamOS | `unarchive` from GitHub releases (no `sudo`) |

## Role Variables

```yaml
nerd_fonts_version: "3.3.0"
nerd_fonts:
  - name: DejaVuSansMono
    creates: DejaVuSansMNerdFontMono-Regular.ttf
  - name: JetBrainsMono
    creates: JetBrainsMonoNerdFontMono-Regular.ttf
```

Add or remove entries from `nerd_fonts` to control which font families are installed.

## Testing

```bash
uv run yamllint .
uv run ansible-lint
uv run molecule test -s localhost
uv run molecule test
```

## Example Playbook

```yaml
- hosts: local
  roles:
    - { role: jahrik.nerd_fonts }
```

## License

GPLv2
