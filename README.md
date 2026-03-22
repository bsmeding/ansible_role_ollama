# Ansible Role: Ollama

![test status](https://github.com/bsmeding/ansible_role_ollama/actions/workflows/ci.yml/badge.svg)

This role deploys [Ollama](https://ollama.com) on a Linux server using the official installation script. Ollama runs local LLMs (Llama, Mistral, etc.) for inference.

## Requirements

- Linux (Debian, Ubuntu, RHEL, Fedora, Arch)
- systemd (for service management)
- curl, zstd (installed by the role when `ollama__install_prerequisites` is true)

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

## Example Playbook

```yaml
- hosts: ollama_servers
  roles:
    - role: bsmeding.ollama
      vars:
        ollama__version: "0.5.7"  # optional: pin version
        ollama__enabled: true
        ollama__port: 11435  # optional: custom port
        ollama__host: 0.0.0.0  # optional: bind to all interfaces for remote access
        ollama__models:
          - llama3.2
          - mistral
          - codellama
```

For Open WebUI (web interface), use the separate `bsmeding.openwebui` role.

## GPU Support

The official install script automatically detects and configures:

- **NVIDIA GPU**: Installs CUDA drivers when detected.
- **AMD GPU**: Downloads and installs ROCm-enabled Ollama build.
- **CPU-only**: Runs in CPU mode when no GPU is detected.

## Post-Install

After deployment, the Ollama API is available at `http://<host>:<port>` (default: `http://127.0.0.1:11434`). Use `ollama__host` and `ollama__port` to customize. Models listed in `ollama__models` are pulled automatically; you can also pull additional models manually:

```bash
ollama pull llama3.2
```

## License

MIT

## Author Information

This role was created by Bart Smeding.
