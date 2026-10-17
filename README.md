# Deezer Arch Linux package

These files are build instructions for the Arch Linux build system how to create an installable Deezer package for the desktop application. However, the patches are not specific to Archlinux, and can be used for other Linux environments.

For Windows, Deezer distributes a version of the Electron run time (Windows binary) and the source code of their application itself. The build process of this package extracts the application source from the Windows installer.

This package applies several patches for:

- Compatibility with newer Electron versions
- Compatibility with a Linux environment in general.
- Fixing bugs

Deezer 8.0.10 runs on Electron 44 with tray controls, smaller windows, and
five-percent volume steps when scrolling over the volume button or slider.
MPRIS provides metadata, playback controls, seeking, volume, shuffle, repeat,
and window activation as the `deezer` player.

To install on Arch Linux, use your favourite AUR helper or build manually with:
```
git clone https://github.com/SibrenVasse/deezer
cd deezer
makepkg -si
```

## Usage

| Option | Description |
| :--- | :--- |
| `--start-in-tray` | Start the app in the tray (see [patch](https://github.com/aunetx/deezer-linux/blob/master/patches/01-start-in-tray.patch)) |
| `--disable-systray` | Quit the app when the window is closed (see [patch](./02-start-without-tray.patch)) |
| `--keep-kernel` | Report the actual numeric kernel version instead of `6.4.0` (see [patch](./04-remove-os-information.patch)) <br> *This feature impacts privacy.* |
| `--hide-offline-banner` | Hide the "Application is offline" banner that appears when using a VPN or DNS blocker (see [patch](./08-hide-offline-banner.patch)) |
| `--disable-animations` | Disable animations (see [patch](./09-disable-animations.patch)) |
| `--disable-notifications` | Disable notifications (see [patch](./10-disable-notifications.patch)) |
| `--sync-theme` | Sync the app's theme (dark/light) to the OS theme (see [patch](./15-sync-theme.patch)) |
| `--custom-theme=<name-or-path>` | Load a packaged theme by filename or a CSS file by path; reload it when the file changes (see [patch](./16-custom-theme.patch)) |
| `--disable-hardware-acceleration` | Disable hardware acceleration (useful for systems with GPU issues) (see [patch](./13-disable-hardware-acceleration.patch)) |
| `--log-level=<level>` | Set the log level (`silly`,`debug`,`verbose`,`info`,`warn`,`error`) (see [patch](https://github.com/aunetx/deezer-linux/blob/master/patches/06-control-log-level.patch)) |
| `--enable-wayland-ime` | Enable IME keyboard support on Wayland (use with `--ozone-platform-hint=auto --wayland-text-input-version=3`) |

| Environment variable | Options | Description |
| :--- | :--- | :--- |
| `DZ_START_IN_TRAY` | `yes`,`no` | Start the app in the tray (see [patch](https://github.com/aunetx/deezer-linux/blob/master/patches/01-start-in-tray.patch)) |
| `DZ_DISABLE_SYSTRAY` | `yes`,`no` | Quit the app when the window is closed (see [patch](./02-start-without-tray.patch)) |
| `DZ_KEEP_KERNEL` | `yes`,`no` | Report the actual numeric kernel version instead of `6.4.0` (see [patch](./04-remove-os-information.patch)) |
| `DZ_LOG_LEVEL` | `silly`...`error` | Set the log level (see [patch](https://github.com/aunetx/deezer-linux/blob/master/patches/06-control-log-level.patch)) |
| `DZ_HIDE_OFFLINE_BANNER` | `yes`,`no` | Hide the "Application is offline" banner (see [patch](./08-hide-offline-banner.patch)) |
| `DZ_DISABLE_ANIMATIONS` | `yes`,`no` | Disable animations (see [patch](./09-disable-animations.patch)) |
| `DZ_DISABLE_NOTIFICATIONS` | `yes`,`no` | Disable notifications (see [patch](./10-disable-notifications.patch)) |
| `DZ_DEVTOOLS` | `yes`,`no` | Enable the developer console (ctrl+shift+i) |
| `DZ_SYNC_THEME` | `yes`,`no` | Sync the app's theme to the OS theme (see [patch](./15-sync-theme.patch)) |
| `DZ_CUSTOM_THEME` | `theme.css` or `/path/to/theme.css` | Load a packaged theme or custom CSS file and reload it when the file changes (see [patch](./16-custom-theme.patch)) |
| `DZ_DISABLE_HARDWARE_ACCELERATION` | `yes`,`no` | Disable hardware acceleration (see [patch](./13-disable-hardware-acceleration.patch)) |
| `DZ_TRAY_ICON` | `/path/to/icon` | Force a custom icon path for the systray (see [patch](./99-systray-icon.patch)) |

## Custom themes

Themes are CSS files loaded when Deezer runs. The app watches the selected file
and applies saved changes without a restart. These instructions are adapted from
[deezer-linux](https://github.com/aunetx/deezer-linux/blob/fcf1972d0c2195a78d92306b5a2ddadd1965e714/README.md#custom-themes).

### Packaged themes

The package installs the themes and a commented template in
`/usr/share/deezer/themes/`:

| Theme | File | Modes |
| :--- | :--- | :--- |
| Solarized | `solarized.css` | Light & dark |
| Nord | `nord.css` | Light & dark |
| Everforest | `everforest.css` | Light & dark |
| Monokai | `monokai.css` | Light & dark |
| SynthWave '84 | `synthwave.css` | Dark only |
| Primer | `primer.css` | Light & dark |
| Gruvbox | `gruvbox.css` | Light & dark |
| Matrix | `matrix.css` | Dark only |
| Liquid Glass | `liquid-glass.css` | Light & dark |

Choose a theme when launching Deezer:

```sh
deezer-desktop --sync-theme --custom-theme=solarized.css
# Or select a theme through the environment:
DZ_CUSTOM_THEME=nord.css deezer-desktop --sync-theme
```

With `--sync-theme`, the light/dark mode follows your desktop preference.
Otherwise, choose the mode in Deezer's settings. For a dark-only theme, select
dark mode in Deezer and omit `--sync-theme` if your desktop uses light mode.

### Create or edit a theme

Copy `template.css` or one of the packaged themes into your home directory so
you can edit it and package updates will leave your changes intact:

```sh
mkdir -p "$HOME/.config/deezer"
cp /usr/share/deezer/themes/template.css "$HOME/.config/deezer/my-theme.css"
deezer-desktop --sync-theme --custom-theme="$HOME/.config/deezer/my-theme.css"
```

Edit and save the file while Deezer remains open to see the result. The template
contains the CSS variables and their initial values; uncomment and change the
ones you need. The custom stylesheet is appended after the page's styles.
Ordinary CSS specificity and `!important` rules still apply.

Scope a rule to the current mode using the root element's `data-theme` attribute:

```css
[data-theme="dark"] body {
  background-color: #002b36;
  color: #839496;
}

[data-theme="light"] body {
  background-color: #fdf6e3;
  color: #657b83;
}
```

The `--custom-theme` option overrides `DZ_CUSTOM_THEME`. A bare filename such as
`solarized.css` uses a file in the launch directory first, then falls back to
`/usr/share/deezer/themes/` if a packaged theme exists. Explicit relative paths
such as `./solarized.css` and absolute paths are used directly. Quote paths
containing spaces.
To select another file or stop using a theme, quit Deezer from the tray and
relaunch with the desired options.

### Inspect styles and troubleshoot

Start Deezer with developer tools enabled:

```sh
DZ_DEVTOOLS=yes deezer-desktop --custom-theme="$HOME/.config/deezer/my-theme.css"
```

Press `Ctrl`+`Shift`+`I` and use the element picker to inspect selectors and CSS
variables. Prefer CSS variables and stable structural selectors: generated class
names may change when Deezer updates. If a file cannot be loaded, the terminal
output includes an error tagged `[custom-theme]`. Check the path and file
permissions; creating or restoring the file lets the watcher retry automatically.

## Debugging
Run the application from the command line with debug logging enabled:
```
deezer-desktop --log-level=debug
```

To run the application with devtools by running
```
env DZ_DEVTOOLS=yes electron44 /usr/share/deezer/app.asar
```

To debug node, you can extract the source files to a directory and inspect the node process by attaching using the chromium debugging tools. (https://www.electronjs.org/docs/tutorial/debugging-main-process)
```
asar extract /usr/share/deezer/app.asar $dest
electron44 --inspect-brk=$port $dest
```