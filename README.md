# Deezer Arch Linux package

These files are build instructions for the Arch Linux build system how to create an installable Deezer package for the desktop application. However, the patches are not specific to Archlinux, and can be used for other Linux environments.

For Windows, Deezer distributes a version of the Electron run time (Windows binary) and the source code of their application itself. The build process of this package extracts the application source from the Windows installer.

This package applies several patches for:
- Compatibility with newer Electron versions 
- Compatibility with a Linux environment in general.
- Fixing bugs

To install on Arch Linux, use your favourite AUR helper or build manually with:
```
git clone https://github.com/SibrenVasse/deezer
cd deezer
makepkg -si
```

## Usage

| Option | Description |
| :--- | :--- |
| `--start-in-tray` | Start the app in the tray (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/01-start-in-tray.patch)) |
| `--disable-systray` | Quit the app when the window is closed (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/02-start-without-tray.patch)) |
| `--keep-kernel` | Use the exact kernel version (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/04-remove-os-information.patch)) <br> *This feature impacts privacy.* |
| `--disable-features` | Disable some features (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/05-provide-metadata-mpris.patch)) |
| `--hide-offline-banner` | Hide the "Application is offline" banner that appears when using a VPN or DNS blocker (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/08-hide-offline-banner.patch)) |
| `--disable-animations` | Disable animations (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/09-disable-animations.patch)) |
| `--disable-notifications` | Disable notifications (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/10-disable-notifications.patch)) |
| `--log-level` | Set the log level (`silly`,`debug`,`verbose`,`info`,`warn`,`error`) (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/06-control-log-level.patch)) |
| `--enable-wayland-ime` | Enable IME keyboard support on Wayland (use with `--ozone-platform-hint=auto --wayland-text-input-version=3`) |

| Environment variable | Options | Description |
| :--- | :--- | :--- |
| `DZ_START_IN_TRAY` | `yes`,`no` | Start the app in the tray (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/01-start-in-tray.patch)) |
| `DZ_DISABLE_SYSTRAY` | `yes`,`no` | Quit the app when the window is closed (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/02-start-without-tray.patch)) |
| `DZ_KEEP_KERNEL` | `yes`,`no` | Use the exact kernel version (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/04-remove-os-information.patch)) |
| `DZ_LOG_LEVEL` | `silly`...`error` | Set the log level (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/06-control-log-level.patch)) |
| `DZ_HIDE_OFFLINE_BANNER` | `yes`,`no` | Hide the "Application is offline" banner (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/08-hide-offline-banner.patch)) |
| `DZ_DISABLE_ANIMATIONS` | `yes`,`no` | Disable animations (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/09-disable-animations.patch)) |
| `DZ_DISABLE_NOTIFICATIONS` | `yes`,`no` | Disable notifications (see [upstream patch](https://github.com/aunetx/deezer-linux/blob/master/patches/10-disable-notifications.patch)) |
| `DZ_DEVTOOLS` | `yes`,`no` | Enable the developer console (ctrl+shift+i) |
| `DZ_TRAY_ICON` | `/path/to/icon` | Force a custom icon path for the systray (see [patch](./99-systray-icon.patch)) |

## Debugging
Running the application from the commandline will show verbose logging.
```
deezer-desktop
```

To run the application with devtools by running
```
env DZ_DEVTOOLS=yes electron38 /usr/share/deezer/app.asar
```

To debug node, you can extract the source files to a directory and inspect the node process by attaching using the chromium debugging tools. (https://www.electronjs.org/docs/tutorial/debugging-main-process)
```
asar extract /usr/share/deezer/app.asar $dest
electron38 --inspect-brk=$port $dest
```
