# Maintainer: Sibren Vasse <arch@sibrenvasse.nl>
# Contributor: Ilya Gulya <ilyagulya@gmail.com>
pkgname="deezer"
pkgver=7.0.130
pkgrel=1
pkgdesc="A proprietary music streaming service"
arch=('any')
url="https://www.deezer.com/"
license=('custom:"Copyright (c) 2006-2024 Deezer S.A."')
depends=('electron36')
provides=('deezer')
makedepends=('p7zip' 'asar' 'prettier>=3.0.0' 'imagemagick')
source=("$pkgname-$pkgver-setup.exe::https://www.deezer.com/desktop/download/artifact-win32-x86-$pkgver"
    "$pkgname.desktop"
    deezer
    remove-kernel-version-from-user-agent.patch
    avoid-change-default-texthtml-mime-type.patch
    start-hidden-in-tray.patch
    systray.patch
    systray-buttons-fix.patch
    quit.patch)
sha256sums=('f86b381ba1941c94b722cafb903649299ff9cb7aa942f89554b1b0f86f693303'
            'c16cf96707c6c047e5f2ec336ce3c639ecf2fc207ff9db365b17363d13380d2c'
            'df3b8694bb62dc3d5a0cb18a1f4aef31432ccb339eb96b522397a9540e6bd613'
            '8eddebb9274e66051b55728e3b73263c0a2d288f70fc6c15917a604a08f7f705'
            'ea846387f2cda84ae152d034f682d2efc87559b5ef7a83eeca8884b49ff2940b'
            '08c9c6276b26da000562a2506694cc76f6799a693eb9e977b5e332db43abe01f'
            'df910c26b0f36bf441c7c1fbea67c0e4f8fea801842705e2adbe636d6b8d244b'
            '90a797525021a6a55e16d15f962ade80f7f1f84aab00ce6b577f539c42fb1029'
            '78d26c08c234594eeba0ac68c95612a8c01ea4026f34e0141e8a997287b0af1b')

prepare() {
    # Extract app from installer
    7z x -so $pkgname-$pkgver-setup.exe "\$PLUGINSDIR/app-32.7z" >app-32.7z
    # Extract app archive
    7z x -y -bsp0 -bso0 app-32.7z

    # Extract png from ico container
    magick resources/win/app.ico resources/win/deezer.png

    cd resources/
    asar extract app.asar app

    cd "$srcdir/resources/app"
    mkdir -p resources/linux/
    install -Dm644 "$srcdir/resources/win/systray.png" resources/linux/

    prettier --write "build/*.js"

    local src
    for src in "${source[@]}"; do
      src="${src%%::*}"
      src="${src##*/}"
      [[ $src = *.patch ]] || continue
      echo "Applying patch ${src}..."
      patch -Np1 < "${srcdir}/${src}"
    done

    cd "$srcdir/resources/"
    asar pack app app.asar
}

package() {
    mkdir -p "$pkgdir/usr/share/deezer"
    mkdir -p "$pkgdir/usr/share/applications"
    mkdir -p "$pkgdir/usr/bin/"
    for size in 16 32 48 64 128 256; do
        mkdir -p "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/"
    done

    install -Dm644 resources/app.asar "$pkgdir/usr/share/deezer/"
    install -Dm644 resources/win/deezer-0.png "$pkgdir/usr/share/icons/hicolor/16x16/apps/deezer.png"
    install -Dm644 resources/win/deezer-1.png "$pkgdir/usr/share/icons/hicolor/32x32/apps/deezer.png"
    install -Dm644 resources/win/deezer-2.png "$pkgdir/usr/share/icons/hicolor/48x48/apps/deezer.png"
    install -Dm644 resources/win/deezer-3.png "$pkgdir/usr/share/icons/hicolor/64x64/apps/deezer.png"
    install -Dm644 resources/win/deezer-4.png "$pkgdir/usr/share/icons/hicolor/128x128/apps/deezer.png"
    install -Dm644 resources/win/deezer-5.png "$pkgdir/usr/share/icons/hicolor/256x256/apps/deezer.png"
    install -Dm644 "$pkgname.desktop" "$pkgdir/usr/share/applications/"
    install -Dm755 deezer "$pkgdir/usr/bin/"
}
