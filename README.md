# Yosecula Tools

Optional desktop fonts and an array-language Matrix rain screensaver for
[Omarchy](https://omarchy.org/), maintained separately from the
[Yosecula visual theme](https://github.com/codereport/omarchy-yosecula-theme).

Installing the theme alone applies its palette and wallpaper. These tools install
the additional desktop behavior explicitly.

## Install

Keep this checkout at `~/Projects/omarchy-yosecula-tools`: the installed
theme-change hooks use that path.

```bash
mkdir -p "$HOME/Projects"
git clone https://github.com/codereport/omarchy-yosecula-tools "$HOME/Projects/omarchy-yosecula-tools"
python3 "$HOME/Projects/omarchy-yosecula-tools/fonts.py"
python3 "$HOME/Projects/omarchy-yosecula-tools/screensaver.py"
```

Run from a logged-in Omarchy desktop. The installers audit existing settings,
install missing dependencies, and back up changed files and font settings under
`~/.local/state/yosecula/backups/`. They preserve other shell settings, bar widgets,
menu entries, and Hyprland rules. Rerunning them repairs drift without rewriting
settings that already match.

[post-omarchy-install](https://github.com/codereport/post-omarchy-install) manages
this checkout and runs both installers through `up` as part of Essential. When
upgrading from the former theme bundle, it installs these tools and replaces the
old hooks before updating the theme. Existing fonts, rain, and idle settings stay
installed.

Audit without applying changes:

```bash
python3 fonts.py --check
python3 screensaver.py --check
python3 screensaver.py --check --json
```

Both installers support `--check --json` for machine-readable state and
explanations. For a manual update, pull this checkout with `git pull --ff-only`,
then rerun the installers.

## Desktop fonts

`fonts.py` sets regular and monospace GTK app text to **JetBrainsMono Nerd Font
11**, with **JetBrainsMono Nerd Font Bold 11** title bars. It installs
`ttf-jetbrains-mono-nerd` when needed. A `theme-set` hook reapplies these settings
when Yosecula is selected. Reopen applications that cache their fonts.

## Matrix screensaver

`screensaver.py` installs the launchers, artwork, menu action, dependencies, and
custom idle plugin. The screensaver shows white rain over the current desktop
wallpaper, without blur or dimming, using Foot's software renderer at 60 FPS.
The white palette keeps trailing glyphs white as well.

Rain starts after 10 idle minutes and continues until automatic locking at 30
idle minutes. VLC playback inhibits both timers; pausing or stopping restores
normal idle behavior. Matrix remains configured when switching themes. Launch
it manually with `~/.local/bin/omarchy-launch-matrix-screensaver force`.

The installer supplies a transparent Foot config and
`~/.config/hypr/matrix-screensaver.lua`. Hyprland keeps desktop windows and the bar
hidden while reporting a windowed state to Foot, preserving transparency over
the wallpaper. The installer reloads Hyprland when these rules change.

The rain uses 305 distinct symbols from Dyalog APL, Kap, BQN, Uiua, and TinyAPL,
collected from [Array Box](https://github.com/codereport/array-box). The included
APL387 and Uiua386 fonts cover the symbols; font attribution and licenses are in
`screensaver/matrix/fonts/LICENSE.md`.

## Palettes and glyphs

`matrix.json` defines the white rain, tips, and final text. The installer copies
it to `~/.config/omarchy/matrix/palettes/yosecula.json`; no screensaver files are
required in the visual theme repository.

At each animation cycle, the launcher first checks the active theme's
`matrix.json`. When that file is absent and Yosecula is active, it uses the
installed companion palette. Missing or invalid palettes otherwise use Matrix's
green defaults. Custom palettes contain `rain_color_gradient`, `highlight_color`,
and `final_gradient_stops` with six-digit RGB hex colors.

`screensaver/matrix/glyphs.json` is the committed symbol snapshot. Refresh it
from a neighboring Array Box checkout with:

```bash
node screensaver/matrix/update-glyphs.mjs ../array-box
```

Node is only needed for development and some tests; the installed screensaver
uses the committed data.

## Development

```bash
python3 -m unittest discover -s tests -v
```

These tools were split from `codereport/omarchy-yosecula-theme`. The Matrix
launchers and idle service derive from Omarchy; their MIT licenses remain in
`screensaver/matrix/` and `screensaver/cph.idle/`. This repository is MIT licensed.
