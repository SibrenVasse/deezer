# Maintainer: Sibren Vasse <arch@sibrenvasse.nl>
# Contributor: Ilya Gulya <ilyagulya@gmail.com>
pkgname="deezer"
pkgver=6.0.290
pkgrel=1
pkgdesc="A proprietary music streaming service"
arch=('any')
url="https://www.deezer.com/"
license=('custom:"Copyright (c) 2006-2024 Deezer S.A."')
depends=('electron13-bin')
provides=('deezer')
makedepends=('p7zip' 'asar' 'prettier>=3.0.0' 'imagemagick')
source=("$pkgname-$pkgver-setup.exe::https://www.deezer.com/desktop/download/artifact/win32/x86/$pkgver"
    "$pkgname.desktop"
    deezer
    remove-kernel-version-from-user-agent.patch
    avoid-change-default-texthtml-mime-type.patch
    start-hidden-in-tray.patch
    quit.patch)
sha256sums=('a7016ae10449be73deab0fecc7e4cd301558d505953c6754e5af9584652031dc'
            'c16cf96707c6c047e5f2ec336ce3c639ecf2fc207ff9db365b17363d13380d2c'
            'b464dbfc0d426730259ce2abc660960ecc6a73bbef838b8fbbd15deb2f5dce53'
            '8eddebb9274e66051b55728e3b73263c0a2d288f70fc6c15917a604a08f7f705'
            '3566f3aa580b35a5af7f31831c36d579576a47286103a9b621051a8a38ac3180'
            '505bc0363443f8a921361efb5f204a15afa7773e57fa386c2221ba1609b896c8'
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
