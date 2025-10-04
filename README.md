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

| Option                                                                               | Description                                                                                                                            |
| ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------|
| `--start-in-tray`                                                                    | Start the app in the tray (see [patch](./01-start-hidden-in-tray.patch))                                                               |
| `--disable-systray`                                                                  | Quit the app when the window is closed (see [patch](./03-quit.patch))                                                                  |
| `--keep-kernel`                                                                      | Use the exact kernel version (see [patch](./05-remove-os-information.patch)) <br/> _This feature impacts privacy._                     |
| `--disable-features`                                                                 | Disable some features (see [patch](./06-better-management-of-MPRIS.patch))                                                             |
| `--hide-appoffline-banner`                                                           | Hide the "Application is offline" banner that appears when using a VPN or DNS blocker (see [patch](./11-hide-appoffline-banner.patch)) |
| `--disable-animations`                                                               | Disable animations (see [patch](./12-disable-animations.patch))                                                                        |
| `--disable-notifications`                                                            | Disable notifications (see [patch](./13-disable-notifications.patch))                                                                  |
| `--enable-wayland-ime` `--ozone-platform-hint=auto` `--wayland-text-input-version=3` | Enable IME keyboard support on Wayland                                                                                                 |

| Environment variable        | Options                                            | Description                                                                                       |
| --------------------------- | -------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| `DZ_HIDE_APPOFFLINE_BANNER` | `yes`,`no`                                         | Hide the "Application is offline" banner (see [patch](./11-hide-appoffline-banner.patch)) |
| `DZ_DISABLE_ANIMATIONS`     | `yes`,`no`                                         | Disable animations (see [patch](./12-disable-animations.patch))                           |
| `DZ_DISABLE_NOTIFICATIONS`  | `yes`,`no`                                         | Disable notifications (see [patch](./13-disable-notifications.patch))                     |
| `LOG_LEVEL`                 | `silly`,`debug`,`verbose`,`info`,`warning`,`error` | Set the log level (see [patch](./07-log-level-environment-variable.patch))                |
| `DZ_DEVTOOLS`               | `yes`,`no`                                         | Enable the developer console (ctrl+shift+i)                                               |
| `DEEZER_TRAY_ICON`          | `/path/to/icon.png`                                | Run with custom systray icon [patch](./15-systray-icon.patch)                             |

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
