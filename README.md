# fuzzel

## Overview

Configuration for [fuzzel](https://codeberg.org/dnkl/fuzzel), a lightweight Wayland application launcher, used as niri's `Mod+D` launcher and also invoked internally by other scripts (e.g. clipboard/emoji pickers if configured). It replaces `rofi` from the ricing's earlier Hyprland-era setup.

## Design Philosophy

- **Single-file, single-purpose.** The entire configuration is one INI file — a launcher has a small surface area and does not warrant a directory structure.
- **Terminal-consistent typography and behavior.** `terminal=kitty -e` and `font=JetBrainsMonoNL Nerd Font Mono` mirror the terminal config exactly, so launching an app from fuzzel feels visually continuous with the terminal it may open into.
- **Flat, rectangular chrome.** `radius=0` and `selection-radius=0` keep fuzzel consistent with the ricing's global rejection of rounded corners.
- **fzf-style matching.** `match-mode=fzf` trades exact-substring matching for fuzzy scoring, prioritizing speed of recall over precision.

## Key Features

- Centered overlay window, 35 columns wide, 10 visible result lines.
- fzf-style fuzzy matching with a live match counter.
- `exit-on-keyboard-focus-loss=yes` — closes automatically when focus moves elsewhere, avoiding orphaned launcher windows.
- Full Imperator color mapping across background, text, prompt, selection, and border, all with alpha channels (`f2`/`ff` suffixes).

## Configuration Breakdown

| Section | Responsibility | Impact |
|---|---|---|
| Top-level keys (`font`, `prompt`, `match-mode`, `terminal`, `anchor`, `lines`, `width`, padding/spacing) | Typography, matching behavior, window geometry | Governs the base look and feel and how `terminal=kitty -e` apps get launched |
| `[colors]` | Background, text, message, prompt, placeholder, input, match/selection highlighting, counter, border — all as `RRGGBBAA` hex | Full visual theme; every color ties back to the Imperator palette (Deep Void background, Golden Signal prompt/match, Amber Light text) |
| `[border]` | Border width and corner radius | `radius=0`/`selection-radius=0` enforce the flat rectangular aesthetic |

## Dependencies

- `fuzzel`
- `kitty` — configured as the terminal for entries that need one (`terminal=kitty -e`)
- A Nerd Font (`JetBrainsMonoNL Nerd Font Mono`) for icon glyphs in results

## Usage

Deployed to the standard fuzzel config location and read on every launch (fuzzel does not run as a persistent daemon in this setup — niri spawns it on demand via `Mod+D { spawn "fuzzel"; }`).

## Customization

- **Appearance**: `[colors]` and `[border]` sections — every value is a direct hex/geometry override, no theme-file indirection.
- **Matching behavior**: `match-mode` (`fzf`, `exact`, or fuzzel's other supported modes) and `match-counter`.
- **Result list size**: `lines`, `width`, `line-height` for how many/how wide entries appear.

## Performance Considerations

- fuzzel is a short-lived process spawned on demand, not a resident daemon — there is no idle memory or CPU cost between invocations.
- `layer=overlay` places it above other layer-shell surfaces without requiring compositor-side window-management logic.
- Fuzzy matching (`match-mode=fzf`) is O(n) over the visible application list per keystroke, which is negligible at desktop-application-count scale.

## Notes

- `icon-theme=default` relies on whatever icon theme is currently active system-wide (the Imperator icon theme under `theme/icons/`), not a fuzzel-specific icon set — if that theme is not installed, entries fall back to generic icons rather than failing.
- Colors use 8-digit hex (`RRGGBBAA`); omitting the alpha channel on a new color entry will be rejected or misinterpreted by fuzzel, unlike some other config formats in this repo that accept 6-digit hex.
