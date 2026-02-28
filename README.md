# mosaico-rules

Community-maintained window rules for [Mosaico](https://github.com/jmelosegui/mosaico), a tiling window manager written in Rust.

## What are rules?

Rules tell Mosaico which windows to tile and which to leave alone. Each rule matches windows by class name or title and sets `manage = true` (tile) or `manage = false` (ignore). Rules are evaluated in order — the first match wins. Windows that match no rule are tiled by default.

## Repository structure

Rules are organized by operating system:

```
windows/rules.toml   # Windows-specific rules
linux/rules.toml     # Linux-specific rules
macos/rules.toml     # macOS-specific rules
```

## How it works

When the Mosaico daemon starts, it downloads the rules file for the current platform from this repository and caches it at `~/.config/mosaico/rules.toml`. This file is overwritten on every startup to keep rules up to date.

Personal overrides go in `~/.config/mosaico/user-rules.toml`, which is never overwritten. User rules take priority over community rules.

## Rule format

```toml
# Match by window class name (exact, case-insensitive)
[[rule]]
match_class = "ApplicationFrameWindow"
manage = false

# Match by window title (substring, case-insensitive)
[[rule]]
match_title = "GlobalProtect"
manage = false

# Both fields can be combined (AND logic)
[[rule]]
match_class = "Chrome_WidgetWin_1"
match_title = "DevTools"
manage = false
```

**Tip:** Run `mosaico debug list` to see the class name and title of every visible window.

## Contributing

1. Fork this repository
2. Add your rule to the appropriate OS file (e.g. `windows/rules.toml`)
3. Include a short comment explaining what the rule does
4. Open a pull request

Before submitting, please verify your rule:

- Run `mosaico debug list` to confirm the exact class name or title
- Test the rule locally by adding it to `~/.config/mosaico/user-rules.toml` first
- Make sure the rule is specific enough — avoid matching generic classes like `#32770` (the standard Win32 dialog class) which would affect many unrelated windows

## License

[MIT](LICENSE)
