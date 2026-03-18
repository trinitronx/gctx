# gctx

Faster way to switch between named configurations in gcloud. Inspired by [kubectx](https://github.com/ahmetb/kubectx).

![Apache 2.0 License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)

## What is it?

`gctx` is a command-line tool that helps you switch between [gcloud named configurations](https://cloud.google.com/sdk/docs/configurations) quickly. If you manage multiple GCP projects or accounts, `gctx` makes it easy to jump between them — with interactive fuzzy search via `fzf` or direct command-line usage.

## Features

- **List** all named configurations (current config highlighted)
- **Switch** to a configuration by name
- **Switch back** to the previous configuration with `gctx -`
- **Rename** configurations
- **Delete** one or more configurations
- **Unset** the current active configuration
- **Interactive mode** with [fzf](https://github.com/junegunn/fzf) when run without arguments in a terminal

## Dependencies

| Dependency | Required | Purpose |
|------------|----------|---------|
| [gcloud CLI](https://cloud.google.com/sdk/docs/install) | Yes | Google Cloud SDK |
| [jq](https://jqlang.github.io/jq/download/) | Yes | JSON parsing (used by `--current`) |
| [fzf](https://github.com/junegunn/fzf#installation) | No | Interactive fuzzy selection |
| bash 4+ | Yes | Shell runtime |

## Installation

### Manual

Download the `gctx` script and place it somewhere on your `PATH`:

```bash
curl -Lo ~/.local/bin/gctx https://raw.githubusercontent.com/trinitronx/gctx/main/gctx
chmod +x ~/.local/bin/gctx
```

### From source

```bash
git clone https://github.com/trinitronx/gctx.git
cd gctx
install -m 0755 gctx /usr/local/bin/gctx
```

## Usage

```
USAGE:
  gctx                       : list the named configurations
  gctx <NAME>                : switch to named configuration <NAME>
  gctx -                     : switch to the previous named configuration
  gctx -c, --current         : show the current named configuration name
  gctx <NEW_NAME>=<NAME>     : rename named configuration <NAME> to <NEW_NAME>
  gctx <NEW_NAME>=.          : rename current named configuration to <NEW_NAME>
  gctx -d <NAME> [<NAME...>] : delete named configuration <NAME> ('.' for current config)
  gctx -u, --unset           : unset the current named configuration

  gctx -h,--help             : show this message
```

### Examples

```bash
# Interactive mode (requires fzf) — just run with no args:
$ gctx

# List all configurations:
$ gctx | cat

# Switch to a configuration named "prod":
$ gctx prod

# Switch back to the previous configuration:
$ gctx -

# Show the current active configuration:
$ gctx -c

# Rename configuration "old-name" to "new-name":
$ gctx new-name=old-name

# Rename the current configuration to "my-project":
$ gctx my-project=.

# Delete a configuration:
$ gctx -d staging

# Unset the active configuration:
$ gctx -u
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `GCLOUD` | Override the `gcloud` binary path |
| `JQ` | Override the `jq` binary path |
| `CLOUDSDK_CONFIG_DIR` | Override the gcloud config directory (default: `~/.config/gcloud`) |
| `GCTX_CURRENT_FGCOLOR` | Custom foreground color for the active config in list output |
| `GCTX_CURRENT_BGCOLOR` | Custom background color for the active config in list output |
| `GCTX_IGNORE_FZF` | Set to skip interactive fzf mode even when available |
| `NO_COLOR` | Disable colored output (respects [no-color.org](https://no-color.org)) |
| `_GCTX_FORCE_COLOR` | Force enable colored output |
| `DEBUG` | Enable debug output (`set -x`) |

## License

Apache License 2.0 — see [LICENSE](LICENSE) for details.
