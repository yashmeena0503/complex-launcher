# Complex launcher

**c**omplex **l**auncher: a fuzzy app launcher and dmenu replacement that runs in your terminal. Written in Go with [Bubble Tea](https://github.com/charmbracelet/bubbletea).

I couldn't find a single launcher that met my needs. The closest was dmenu with patches. So `cl` is a TUI that works in any terminal emulator.

## Features

- **App launcher**: fuzzy-searches every executable in `$PATH` and launches the selection detached (`setsid`), so it survives the launcher closing.
- **dmenu mode**: pipe in a list, get the selection on stdout.
- **Prefixes**: configurable triggers that turn the launcher into a command palette:
  - `url` opens a templated URL (web searches, GitHub, etc.)
  - `copy` / `copy_and_notify` copy text to the clipboard
  - `calculator` evaluates expressions inline with [expr](https://github.com/expr-lang/expr) and copies the result
- **Copy fallback**: pressing enter with no match copies the typed text and sends a notification.
- **Persistent mode** (`-loop`): the TUI stays alive and hides instead of quitting, so it reopens instantly.
- **Toggle** (`-toggle`): show, hide or launch the launcher window with one keybind (X11).

## Usage

```sh
cl                          # launch apps from $PATH
printf 'a\nb\n' | cl        # dmenu mode, selection printed to stdout
cl -loop -height 10         # persistent, max 10 visible rows
cl -toggle                  # show/hide the launcher window (X11)
```

Keys: `up`/`ctrl+k`, `down`/`ctrl+j`, `enter`, `esc`/`ctrl+c`.

## Config

`~/.config/complex-launcher/config.json`

```json
{
  "terminal_command": "st -c %s -e cl -loop",
  "window_class_name": "complex-launcher",
  "prefixes": [
    {
      "prefix": "gh ",
      "execute_type": "url",
      "items": [
        { "name": "github search", "cmd": "https://github.com/search?q=%s" }
      ]
    },
    { "prefix": "=", "kind": "calculator", "execute_type": "copy_and_notify" }
  ]
}
```

- `terminal_command`: how `-toggle` starts the launcher; `%s` is replaced with `window_class_name`.
- `prefix`: matched against the start of your query. The rest of the query is substituted into the item's `cmd` (`%s`).
- Prefix items are listed in the order defined in the config.

Bind `cl -toggle` to a key in your window manager (e.g. dwm).

## Install

Requires Go 1.26+.

```sh
go build -o cl .
make i    # copies the binary to ~/.local/bin
```

Runtime tools: `xdotool` (toggle / window hiding), `xclip` (copy), `notify-send` (notifications).
