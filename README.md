# Ansible Role: Ollama

![test status](https://github.com/bsmeding/ansible_role_ollama/actions/workflows/ci.yml/badge.svg)

This role deploys [Ollama](https://ollama.com) on a Linux server using the official installation script. Ollama runs local LLMs (Llama, Mistral, etc.) for inference.

## Requirements

- Linux (Debian, Ubuntu, RHEL, Fedora, Arch)
- systemd (for service management)
- curl, zstd (installed by the role when `ollama__install_prerequisites` is true)
- For Open WebUI: Python 3.11+ on the target host

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable | Default | Description |
|----------|---------|-------------|
| `ollama__skip_setup` | `false` | When true, setup is skipped. |
| `ollama__version` | `''` | Pin Ollama version (e.g., `0.5.7`). Empty = latest. |
| `ollama__install_prerequisites` | `true` | Install curl and zstd before running the install script. |
| `ollama__state` | `started` | Service state: `started`, `stopped`, or `restarted`. |
| `ollama__enabled` | `true` | Enable the ollama service at boot. |
| `ollama__host` | `127.0.0.1` | Bind address (use `0.0.0.0` for remote access). |
| `ollama__port` | `11434` | API listening port. |
| `ollama__models` | `[]` | List of models to pull (e.g., `['llama3.2', 'mistral', 'codellama']`). |
| `ollama__open_webui` | `false` | Install Open WebUI (ChatGPT-like web interface) via pip in user's venv. |
| `ollama__open_webui_version` | `0.8.8` | Pin version. 0.8.10 has broken ddgs dep on PyPI; use 0.8.8 or empty for latest. |
| `ollama__open_webui_install_from_git` | `false` | Install from git main (gets ddgs fix for 0.8.10+). |
| `ollama__open_webui_user` | `ansible_user_id` | User whose home folder gets the venv (`~/.local/open-webui/venv`). |
| `ollama__open_webui_venv_path` | `~/.local/open-webui/venv` | Override venv path (default: in user's home). |
| `ollama__open_webui_port` | `8080` | Port for Open WebUI (e.g. `http://host:8080`). |
| `ollama__open_webui_auth` | `true` | Require login. Set `false` for single-user mode. |

## Example Playbook

```yaml
- hosts: ollama_servers
  roles:
    - role: ansible_role_ollama
      vars:
        ollama__version: "0.5.7"  # optional: pin version
        ollama__enabled: true
        ollama__port: 11435  # optional: custom port
        ollama__host: 0.0.0.0  # optional: bind to all interfaces for remote access
        ollama__models:
          - llama3.2
          - mistral
          - codellama
        ollama__open_webui: true  # optional: add web UI at http://host:8080
```

## GPU Support

The official install script automatically detects and configures:

- **NVIDIA GPU**: Installs CUDA drivers when detected.
- **AMD GPU**: Downloads and installs ROCm-enabled Ollama build.
- **CPU-only**: Runs in CPU mode when no GPU is detected.

## Open WebUI

When `ollama__open_webui: true`, the role installs [Open WebUI](https://docs.openwebui.com/) via pip in a virtual environment in the user's home folder (`~/.local/open-webui/venv`). Requires Python 3.11+ on the target host. Open WebUI runs as a systemd service and is available at `http://<host>:8080` (or `ollama__open_webui_port`).

Set `ollama__open_webui_user` to the user whose home gets the venv (default: the Ansible user). Set `ollama__open_webui_auth: false` for single-user mode without login.

## Post-Install

After deployment, the Ollama API is available at `http://<host>:<port>` (default: `http://127.0.0.1:11434`). Use `ollama__host` and `ollama__port` to customize. Models listed in `ollama__models` are pulled automatically; you can also pull additional models manually:

```bash
ollama pull llama3.2
```

## License

MIT

## Author Information

This role was created by Bart Smeding.
