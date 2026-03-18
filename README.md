# gctx

Faster way to switch between named configurations in gcloud. Inspired by [kubectx](https://github.com/ahmetb/kubectx).

![Apache 2.0 License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)

## What is it?

`gctx` is a command-line tool that helps you switch between
[gcloud named configurations][gcloud-configs] quickly. If you manage multiple
GCP projects or accounts, `gctx` makes it easy to jump between them — with
interactive fuzzy search via `fzf` or direct command-line usage.

[gcloud-configs]: https://cloud.google.com/sdk/docs/configurations

## Features

- **List** all named configurations (current config highlighted)
- **Switch** to a configuration by name
- **Switch back** to the previous configuration with `gctx -`
- **Rename** configurations
- **Delete** one or more configurations
- **Unset** the current active configuration
- **Interactive mode** with [fzf][fzf] when run
  without arguments in a terminal

[fzf]: https://github.com/junegunn/fzf

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

### [`chezmoi`][chezmoi] dotfiles manager

<!-- markdownlint-disable MD033 -->

Add the following snippet to `.chezmoiexternal.$FORMAT{,.tmpl}` to install via
`chezmoi`'s git-repo external handler:

<details><summary><code>.chezmoiexternal.yaml.tmpl</code></summary>
<p>

Note: Optionally wrap in the `{{ if -}} ... {{ end -}}` block to omit installing
the symlink if `gcloud` or `jq` are not installed.

```gotmpl
{{ if lookPath "gcloud" | and ( lookPath "jq" ) -}}
".local/share/gctx":
  type: "git-repo"
  url: "https://github.com/trinitronx/gctx"
  refreshPeriod: "168h"
  pull:
    args: ["--ff-only"]

{{ end -}}
```

</p>
</details>

Add the following to symlink `gctx` script into `~/.local/bin/gctx`:

<details><summary><code>private_dot_local/bin/symlink_gctx</code></summary>
<p>

```text
../share/gctx/gctx
```

</p>
</details>

Optional: Add the following to `.chezmoiignore` in order to omit installing the
symlink if `gcloud` or `jq` are not installed.

<details><summary><code>.chezmoiignore</code></summary>
<p>

```gotmpl
{{ if not (lookPath "gcloud") | or (not (lookPath "jq")) -}}
.local/bin/gctx
{{ end -}}
```

</p>
</details>

<!-- markdownlint-enable MD033 -->

[chezmoi]: https://www.chezmoi.io/

### From source (for packagers)

```bash
git clone https://github.com/trinitronx/gctx.git
cd gctx
install -m 0755 gctx /usr/local/bin/gctx
```

## Usage

```text
USAGE:
  gctx                       : list the named configurations
  gctx <NAME>                : switch to named configuration <NAME>
  gctx -                     : switch to the previous named configuration
  gctx -c, --current         : show the current named configuration name
  gctx <NEW_NAME>=<NAME>     : rename named configuration <NAME> to <NEW_NAME>
  gctx <NEW_NAME>=.          : rename current named configuration to <NEW_NAME>
  gctx -d <NAME> [<NAME...>] : delete named configuration <NAME>
                                ('.' for current config)
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

<!-- markdownlint-disable MD013 -->

| Variable | Description |
|----------|-------------|
| `GCLOUD` | Override the `gcloud` binary path |
| `JQ` | Override the `jq` binary path |
| `CLOUDSDK_CONFIG_DIR` | Override gcloud config dir (default: `~/.config/gcloud`) |
| `GCTX_CURRENT_FGCOLOR` | Custom foreground color for active config in list output |
| `GCTX_CURRENT_BGCOLOR` | Custom background color for active config in list output |
| `GCTX_IGNORE_FZF` | Skip interactive fzf mode even when available |
| `NO_COLOR` | Disable colored output (respects [no-color.org][no-color]) |
| `_GCTX_FORCE_COLOR` | Force enable colored output |
| `DEBUG` | Enable debug output (`set -x`) |

<!-- markdownlint-enable MD013 -->

[no-color]: https://no-color.org

## License

Apache License 2.0 — see [LICENSE](LICENSE) for details.
