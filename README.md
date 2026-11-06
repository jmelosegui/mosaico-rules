# mosaico-rules

Community-maintained window rules for [Mosaico](https://github.com/jmelosegui/mosaico), a tiling window manager written in Rust.

## What are rules?

Rules tell Mosaico which windows to tile, which to float above the tiles and which to leave alone. Each rule matches windows by class name, title or executable path and sets `action = "tile"`, `"float"` or `"ignore"`. Rules are evaluated in order — the first match wins. Windows that match no rule are tiled by default.

## Repository structure

Rules are organized by operating system:

```
windows/rules-v2.toml   # Windows rules, read by Mosaico with floating windows
windows/rules.toml      # Deprecated: frozen for older Mosaico versions
```

The file name carries the rules format. `rules-v2.toml` uses `action`;
`rules.toml` keeps the older `manage = true/false` format for Mosaico
versions before floating windows, which reject a file with any rule they
cannot read. It no longer gets new rules and will be removed in a future
release. Add new rules to `rules-v2.toml` only.

## How it works

When the Mosaico daemon starts, it downloads the rules file for the current platform from this repository and caches it at `~/.config/mosaico/rules.toml`. This file is overwritten on every startup to keep rules up to date.

Personal overrides go in `~/.config/mosaico/user-rules.toml`, which is never overwritten. User rules take priority over community rules.

## Rule format

```toml
# Match by window class name (exact, case-insensitive)
[[rule]]
match_class = "ApplicationFrameWindow"
action = "ignore"

# Match by window title (substring, case-insensitive)
[[rule]]
match_title = "pinentry"
action = "float"

# Match by executable path (ends with, case-insensitive)
[[rule]]
match_path = "photocraft.exe"
action = "tile"

# Fields can be combined (AND logic)
[[rule]]
match_class = "Chrome_WidgetWin_1"
match_title = "DevTools"
action = "float"
```

| Action | What happens |
|--------|--------------|
| `tile` | Managed and tiled into the layout |
| `float` | Managed, but floats above the tiles of its workspace and moves with it |
| `ignore` | Never managed |

**Tip:** Run `mosaico debug list` to see the class name and title of every visible window.

## Contributing

1. Fork this repository
2. Add your rule to the appropriate OS file (e.g. `windows/rules-v2.toml`)
3. Include a short comment explaining what the rule does
4. Open a pull request

Before submitting, please verify your rule:

- Run `mosaico debug list` to confirm the exact class name or title
- Test the rule locally by adding it to `~/.config/mosaico/user-rules.toml` first
- Make sure the rule is specific enough — avoid matching generic classes such as `Chrome_WidgetWin_1`, which many unrelated apps share. Standard dialogs (`#32770`) already float through their own rule.

## License

[MIT](LICENSE)
