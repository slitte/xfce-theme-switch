# xfce-theme-switch

Automatic dark/light theme switcher for XFCE based on sun position, with a GTK3 system tray.

## Features

- Switches between dark and light theme automatically at sunrise and sunset
- Sun position calculated locally — no internet required for the switch itself
- System tray with left-click quick menu (Auto / Light / Dark) and right-click settings window
- Settings dialog with city search (OpenStreetMap Nominatim) for easy coordinate setup
- Configures GTK3, GTK4, xfwm4, XFCE panel, gsettings, Qt5ct, Qt6ct and VSCode simultaneously
- Manual override persists across timer runs until explicitly reset to Auto
- Debounced switching prevents race conditions when toggling rapidly
- Strict config validation with actionable error messages

## Requirements

- Python ≥ 3.9
- `python-gobject` — GTK3 bindings for the tray
- `xfce4-settings` — provides `xfconf-query` and `xfsettingsd`
- `glib2` — provides `gsettings`
- `procps-ng` — provides `pkill`, `pgrep`

Optional:
- `qt5ct` / `qt6ct` — for Qt application theming
- `matcha-gtk-theme` — default theme used in the example config
- `papirus-icon-theme` — default icon theme used in the example config

## Installation

### Arch Linux (AUR)

```sh
yay -S xfce-theme-switch
```

Or manually:

```sh
git clone https://aur.archlinux.org/xfce-theme-switch.git
cd xfce-theme-switch
makepkg -si
```

### Manual

```sh
install -Dm755 bin/xfce-theme-switch ~/.local/bin/xfce-theme-switch
install -Dm755 bin/xfce-theme-tray   ~/.local/bin/xfce-theme-tray

install -Dm644 systemd/xfce-theme-switch.service \
    ~/.config/systemd/user/xfce-theme-switch.service
install -Dm644 systemd/xfce-theme-switch.timer \
    ~/.config/systemd/user/xfce-theme-switch.timer
install -Dm644 systemd/xfce-theme-tray.service \
    ~/.config/systemd/user/xfce-theme-tray.service
```

## Setup

**1. Create the default config:**

```sh
xfce-theme-switch
```

This creates `~/.config/xfce-theme-switch/config` and exits. Edit it to set your
coordinates and preferred themes, then run again.

**2. Enable the timer and tray:**

```sh
systemctl --user enable --now xfce-theme-switch.timer
systemctl --user enable --now xfce-theme-tray.service
```

## Configuration

Config file: `~/.config/xfce-theme-switch/config`

```ini
[location]
# Geographic coordinates — use the tray's settings dialog to search by city name
lat = 53.08
lon = 8.80

[dark]
gtk_theme  = Matcha-dark-sea
icon_theme = Papirus-Dark-Maia
wm_theme   = Matcha-dark-sea
qt_style   = adwaita-dark

[light]
gtk_theme  = Matcha-light-sea
icon_theme = Papirus-Light-Maia
wm_theme   = Matcha-light-sea
qt_style   = adwaita
```

All theme names must be directories that exist under `/usr/share/themes`,
`~/.local/share/themes` or `~/.themes` (icons: same with `/usr/share/icons` etc.).
The tool will tell you exactly what is missing and where it searched.

## Usage

### CLI

```sh
xfce-theme-switch                  # apply correct mode based on sun position
xfce-theme-switch --force dark     # force dark mode (one-shot, no state change)
xfce-theme-switch --force light    # force light mode (one-shot, no state change)
xfce-theme-switch --status         # show current mode, override state, sunrise/sunset
xfce-theme-switch --skip-checks    # skip dependency/theme validation (faster for cron)
```

### Tray

| Action | Result |
|---|---|
| Left click | Quick menu: Auto / Light / Dark |
| Right click | Settings window |
| Auto | Follows sunrise/sunset automatically |
| Light / Dark | Override — stays until you select Auto again |

## Override state

The tray writes `~/.config/xfce-theme-switch/state` when you select a mode.
The timer respects this file: if the override is `dark` or `light`, the sun position
is ignored. Set to `auto` (via tray or by deleting the file) to resume automatic switching.

## Troubleshooting

**GTK app bodies don't switch theme:**
Check if `xfsettingsd` is running (`pgrep xfsettingsd`). The tool starts it automatically
if it is missing, but if it was killed externally it may need a manual restart:
```sh
xfsettingsd &
```

**Wrong sunrise/sunset times:**
Verify your coordinates in the config. The tool uses local timezone automatically.

**Theme not found error:**
Install the theme or update the config to use a theme you have installed.
Run `xfce-theme-switch --status` to see what is currently active.

## License

Mozilla Public License 2.0 — see [LICENSE](LICENSE)
