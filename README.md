# Deezer Arch Linux package

These files are build instructions for the Arch Linux build system how to create an installable Deezer package for the desktop application. However, the patches are not specific to Archlinux, and can be used for other Linux environments.

For Windows, Deezer distributes a version of the Electron run time (Windows binary) and the source code of their application itself. The build process of this package extracts the application source from the Windows installer.

This package applies several patches for:
- Compatability with newer Electron versions 
- Compatability with a Linux environment in general.
- Fixing bugs

To install on Arch Linux, use your favourite AUR helper or build manually with:
```
git clone https://github.com/SibrenVasse/deezer
cd deezer
makepkg -si
```

## Debugging
Running the application from the commandline will show verbose logging.
```
deezer
```

To run the application with devtools by running
```
env DZ_DEVTOOLS=yes electron13 /usr/share/deezer/app.asar
```

To debug node, you can extract the source files to a directory and inspect the node process by attaching using the chromium debugging tools. (https://www.electronjs.org/docs/tutorial/debugging-main-process)
```
asar extract /usr/share/deezer/app.asar $dest
electron13 --inspect-brk=$port $dest
```
