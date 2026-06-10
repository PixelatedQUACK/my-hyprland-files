# My Hyprland Files

## Overview

This repository contains a personal Hyprland configuration split into small files by responsibility. The expected runtime location is `~/.config/hypr`, with `hyprland.conf` acting as the entry point and the files under `controls/` and `others/` providing the actual configuration modules.

The setup is aimed at a keyboard-driven Hyprland session with Kitty, Rofi, Waybar, Hyprpaper, Firefox, Yazi, Dolphin, VS Code, and common laptop media controls. It also includes a few machine-specific paths and display assumptions that should be changed before reusing it on another system.

## Repository Layout

| Path | Purpose |
| --- | --- |
| `hyprland.conf` | Main Hyprland entry point. It sources each config module from `~/.config/hypr/controls/`. |
| `controls/autostart.conf` | Programs and services launched once when Hyprland starts. |
| `controls/keybinds.conf` | Variables and keybindings for launchers, window actions, workspaces, screenshots, media keys, and mouse actions. |
| `controls/looks.conf` | Gaps, borders, colors, decoration, blur, animations, layout, and miscellaneous visual behavior. |
| `controls/workspaces.conf` | Window rules for suppressing maximize events, XWayland drag behavior, and the `hyprland-run` window. |
| `controls/input.conf` | Keyboard layout, pointer behavior, touchpad behavior, and a per-device mouse sensitivity override. |
| `controls/envi.conf` | Environment variables for cursor size and Hyprshot screenshot output. |
| `controls/permissions.conf` | Commented Hyprland permission examples. No permissions are actively enforced by this file. |
| `controls/monitors.conf` | Monitor layout for the internal display. |
| `others/hyprpaper.conf` | Hyprpaper wallpaper configuration. |

## Installation And Restore

These files are written to be used from `~/.config/hypr`. From the root of this repository, one safe restore flow is:

```bash
if [ -d ~/.config/hypr ]; then
  mv ~/.config/hypr ~/.config/hypr.backup.$(date +%Y%m%d-%H%M%S)
fi

mkdir -p ~/.config/hypr
cp -r hyprland.conf controls others ~/.config/hypr/
```

After copying the files, reload Hyprland if you are already inside a Hyprland session:

```bash
hyprctl reload
```

Permission changes documented in `controls/permissions.conf` require a full Hyprland restart if you later enable them.

## Dependencies

The configuration references these programs, services, and tools:

| Dependency | Used by |
| --- | --- |
| Hyprland | Main Wayland compositor. |
| Hyprpaper | Wallpaper daemon launched by `controls/autostart.conf`. |
| Waybar | Status bar launched at startup. |
| Kitty | Default terminal and terminal host for Yazi and Neovim. |
| Neovim | Opens `~/.config/hypr` through the config keybinding. |
| Rofi | Application launcher through `rofi -show drun`. |
| Firefox | Browser, Messenger, and YouTube keybindings. |
| Yazi | Terminal file manager. |
| Dolphin | Graphical file manager. |
| Visual Studio Code | Editor launched by the VS Code keybinding. |
| Hyprshot | Region screenshot command. |
| hyprsunset | Gamma adjustment commands bound to brightness keys. |
| brightnessctl | Hardware brightness commands bound to brightness keys. |
| WirePlumber `wpctl` | Volume and microphone media keys. |
| playerctl | Play, pause, next, and previous media keys. |
| polkit-kde-agent | Authentication agent launched at startup. |
| Bluetooth service | Enabled and started from autostart. |
| NetworkManager service | Enabled and started from autostart. |
| `~/Commands/things_rofi.sh` | Custom tools menu launched with `SUPER+O`. |

Package names vary by distribution. The config assumes these commands are available on `PATH` unless a full path is used.

## Main Configuration Flow

`hyprland.conf` only sources module files:

```conf
source = ~/.config/hypr/controls/keybinds.conf
source = ~/.config/hypr/controls/autostart.conf
source = ~/.config/hypr/controls/looks.conf
source = ~/.config/hypr/controls/workspaces.conf
source = ~/.config/hypr/controls/input.conf
source = ~/.config/hypr/controls/envi.conf
source = ~/.config/hypr/controls/permissions.conf
source = ~/.config/hypr/controls/monitors.conf
```

Because the source paths are absolute under `~/.config/hypr`, the repository contents need to be copied or linked into that location for the config to load as written.

## Autostart

`controls/autostart.conf` starts these once per Hyprland session:

| Command | Purpose |
| --- | --- |
| `hyprpaper -c ~/.config/hypr/others/hyprpaper.conf` | Starts Hyprpaper with the wallpaper config in this repo. |
| `waybar &` | Starts Waybar. |
| `systemctl enable --now bluetooth` | Enables and starts Bluetooth. |
| `systemctl enable --now NetworkManager` | Enables and starts NetworkManager. |
| `/usr/lib/polkit-kde-authentication-agent-1` | Starts the KDE Polkit authentication agent. |

The two `systemctl enable --now` commands may require permissions depending on the system. If Bluetooth and NetworkManager are already managed elsewhere, these can be moved out of Hyprland autostart.

## Configuration Details

### Appearance

`controls/looks.conf` configures a compact layout with inner gaps of `2`, outer gaps of `10`, a border size of `2`, and the `dwindle` layout. The active border uses a blue-to-red angled gradient, while inactive borders use `rgba(343434aa)`.

Decoration settings include rounded corners, full opacity for active and inactive windows, disabled shadows, and enabled blur with size `3`, one pass, and vibrancy `0.1696`.

Animations are enabled and define several curves and animation rules for windows, borders, fades, layers, workspaces, and zoom factor. The `dwindle` block enables pseudotiling and preserves splits. The `master` block sets new windows to `master`. The `misc` block leaves Hyprland's default wallpaper behavior enabled with `force_default_wallpaper = -1` and keeps the Hyprland logo enabled.

### Input

`controls/input.conf` sets the keyboard layout to `us`, enables focus-follow-mouse with `follow_mouse = 1`, sets pointer sensitivity to `-0.1`, and enables natural scrolling on the touchpad.

It also includes a per-device block for `epic-mouse-v1` with sensitivity `-0.5`.

### Environment

`controls/envi.conf` sets:

| Variable | Value |
| --- | --- |
| `XCURSOR_SIZE` | `24` |
| `HYPRCURSOR_SIZE` | `24` |
| `HYPRSHOT_DIR` | `/home/keithron/Pictures/Screenshots/` |

The screenshot directory is machine-specific and should be changed if that path does not exist.

### Monitor

`controls/monitors.conf` configures one monitor:

```conf
monitor=eDP-1, 1920x1200@165, 0x0, 1
```

This targets an internal display named `eDP-1` at `1920x1200`, `165 Hz`, positioned at `0x0`, with scale `1`.

### Window Rules

`controls/workspaces.conf` defines three window rules:

| Rule | Behavior |
| --- | --- |
| `suppress-maximize-events` | Ignores maximize requests from all apps. |
| `fix-xwayland-drags` | Prevents focus on a specific floating XWayland edge case with empty class/title. |
| `move-hyprland-run` | Floats `hyprland-run` and moves it near the lower-left area of the monitor. |

### Permissions

`controls/permissions.conf` currently contains comments and example permission rules only. Permission enforcement is not active because the `ecosystem` block and all `permission` entries are commented out.

### Wallpaper

`others/hyprpaper.conf` sets a wallpaper on `eDP-1`:

```conf
wallpaper {
  monitor = eDP-1
  path = ~/Documents/System/Computer/dinobow.png
  fit_mode = cover
}

splash = false
```

Change the monitor name and image path before reuse on a different machine.

## Keybindings

The main modifier is `SUPER`, set in `controls/keybinds.conf` as:

```conf
$mainMod = SUPER
```

### Launchers And Apps

| Binding | Action |
| --- | --- |
| `SUPER+Return` | Open Kitty. |
| `SUPER+A` | Open Rofi with `rofi -show drun`. |
| `SUPER+F` | Open Yazi inside Kitty. |
| `SUPER+G` | Open Dolphin. |
| `SUPER+B` | Open Firefox. |
| `SUPER+V` | Open Visual Studio Code. |
| `SUPER+Insert` | Open `~/.config/hypr` in Neovim inside Kitty. |
| `SUPER+O` | Run `~/Commands/things_rofi.sh`. |
| `SUPER+M` | Open `messenger.com` in Firefox. |
| `SUPER+Y` | Open `youtube.com` in Firefox. |
| `SUPER+P` | Take a region screenshot with Hyprshot. |

### Windows And Focus

| Binding | Action |
| --- | --- |
| `SUPER+X` | Kill the active window. |
| `SUPER+Left` | Move focus left. |
| `SUPER+Right` | Move focus right. |
| `SUPER+Up` | Move focus up. |
| `SUPER+Down` | Move focus down. |
| `ALT+Tab` | Cycle focus to the previous window. |

### Workspaces

| Binding | Action |
| --- | --- |
| `SUPER+1` through `SUPER+9` | Switch to workspaces `1` through `9`. |
| `SUPER+0` | Switch to workspace `10`. |
| `SUPER+Shift+1` through `SUPER+Shift+9` | Move the active window to workspaces `1` through `9`. |
| `SUPER+Shift+0` | Move the active window to workspace `10`. |
| `SUPER+S` | Toggle the special workspace named `magic`. |
| `SUPER+Shift+S` | Move the active window to `special:magic`. |
| `SUPER+Mouse wheel down` | Move to the next existing workspace. |
| `SUPER+Mouse wheel up` | Move to the previous existing workspace. |

### Mouse

| Binding | Action |
| --- | --- |
| `SUPER+Left mouse drag` | Move a window. |
| `SUPER+Right mouse drag` | Resize a window. |

### Laptop And Media Keys

| Binding | Action |
| --- | --- |
| `XF86AudioRaiseVolume` | Raise default sink volume by 5%, capped at 100%. |
| `XF86AudioLowerVolume` | Lower default sink volume by 5%. |
| `XF86AudioMute` | Toggle default sink mute. |
| `XF86AudioMicMute` | Toggle default source mute. |
| `XF86AudioNext` | Next media track through `playerctl`. |
| `XF86AudioPause` | Toggle play/pause through `playerctl`. |
| `XF86AudioPlay` | Toggle play/pause through `playerctl`. |
| `XF86AudioPrev` | Previous media track through `playerctl`. |

### Brightness And Gamma Note

`XF86MonBrightnessUp` and `XF86MonBrightnessDown` are bound twice: once to `hyprctl hyprsunset gamma +10/-10` and once to `brightnessctl ... set 5%+/-`. If both bindings run in your Hyprland version, these keys may adjust both gamma and hardware brightness. If that is not desired, keep only the pair you want.

## Machine-Specific Values

These values are specific to the current machine or user setup:

| Value | Where | What to change |
| --- | --- | --- |
| `eDP-1` | `controls/monitors.conf`, `others/hyprpaper.conf` | Replace with your monitor name from `hyprctl monitors`. |
| `/home/keithron/Pictures/Screenshots/` | `controls/envi.conf` | Replace with an existing screenshot directory for your user. |
| `~/Documents/System/Computer/dinobow.png` | `others/hyprpaper.conf` | Replace with your wallpaper path. |
| `~/Commands/things_rofi.sh` | `controls/keybinds.conf` | Replace with your own script path or remove the `SUPER+O` binding. |
| `epic-mouse-v1` | `controls/input.conf` | Replace with your device name or remove the per-device block. |

## Maintenance Notes

- Add new Hyprland modules under `controls/` and source them from `hyprland.conf`.
- Keep source paths aligned with the expected runtime location under `~/.config/hypr`.
- Use `hyprctl reload` after normal config edits.
- Restart Hyprland after enabling permission enforcement in `controls/permissions.conf`.
- Check monitor names with `hyprctl monitors` before copying this setup to different hardware.
- Keep custom paths in `controls/envi.conf`, `controls/keybinds.conf`, and `others/hyprpaper.conf` updated when moving machines or changing usernames.
