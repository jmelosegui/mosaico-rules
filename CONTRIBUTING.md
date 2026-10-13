# Contributing to mosaico-rules

Thank you for helping improve Mosaico's default window rules! This guide
explains how to add, test, and submit rules.

## Before You Start

1. Install [Mosaico](https://github.com/jmelosegui/mosaico) and run
   `mosaico start` so the daemon is active.
2. Open the application you want to write a rule for.

## Finding Window Properties

Run the debug command to list all visible windows with their class names
and titles:

```sh
mosaico debug list
```

Note the **class name**, **title**, and **path** of the window you want
to target.

## Writing a Rule

Add a `[[rule]]` entry to the appropriate OS file (e.g.
`windows/rules.toml`). Each rule needs:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `match_class` | string | No | Exact class name match (case-insensitive) |
| `match_title` | string | No | Substring title match (case-insensitive) |
| `match_path` | string | No | Executable path match (case-insensitive, ends with, e.g. `"app.exe"`) |
| `manage` | bool | Yes | `false` to exclude, `true` to force-tile |

At least one of `match_class`, `match_title`, or `match_path` is required.
If more than one is specified, all must match (AND logic).

`match_path` compares against the end of the program's full path, starting
at a folder boundary, so `"app.exe"` matches `C:\Vendor\app.exe` but not
`C:\Vendor\myapp.exe`. Older Mosaico versions ignore this field, so a rule
that uses only `match_path` has no effect on them.

Apps that draw their own title bar are not tiled unless a rule opts them in
with `manage = true`. Use `match_path` for these when the app's window class
is generic.

Include a short comment above the rule explaining what it does:

```toml
# Snipping Tool overlay — should float, not tile.
[[rule]]
match_title = "Snipping Tool"
manage = false
```

## Testing Locally

Before submitting, test your rule by adding it to your local
`~/.config/mosaico/user-rules.toml`:

1. Add the rule to `user-rules.toml`
2. The daemon hot-reloads the file automatically (no restart needed)
3. Open the target application and verify it behaves as expected
4. Remove the rule from `user-rules.toml` once confirmed

## Submitting a Pull Request

1. Fork this repository
2. Add your rule to the correct OS file under the appropriate section
3. Keep rules grouped by category (UWP/System, GPU overlays, VPN clients,
   etc.) — add a new section header if needed
4. Open a pull request with a short description of what the rule does

## Guidelines

- **Be specific.** Prefer `match_class` or `match_path` over `match_title`
  when possible, since class names and executables are more stable than
  titles.
- **Avoid overly broad matches.** A rule matching `match_title = "Settings"`
  would exclude any window with "Settings" in the title, not just the
  Windows Settings app.
- **One rule per window type.** Don't combine unrelated windows into a
  single rule.
- **Test before submitting.** Confirm the rule works with `user-rules.toml`
  before opening a PR.
