# Maintainer: Sibren Vasse <arch@sibrenvasse.nl>
# Contributor: Ilya Gulya <ilyagulya@gmail.com>
pkgname="deezer"
pkgver=7.0.140
pkgrel=2
pkgdesc="A proprietary music streaming service"
arch=('any')
url="https://www.deezer.com/"
license=('custom:"Copyright (c) 2006-2024 Deezer S.A."')
depends=('electron37' 'hicolor-icon-theme')
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
sha256sums=('9cbe79e00c915121a90e6d2c28c1f7e6ef41f28f2dfa5b3afb5bea62fd98e6b8'
            'c16cf96707c6c047e5f2ec336ce3c639ecf2fc207ff9db365b17363d13380d2c'
            'b1cc3320f0892478a305dd10cd9d6f079d8708f35fb679b34588e359584102a3'
            '8eddebb9274e66051b55728e3b73263c0a2d288f70fc6c15917a604a08f7f705'
            'ea846387f2cda84ae152d034f682d2efc87559b5ef7a83eeca8884b49ff2940b'
            '08c9c6276b26da000562a2506694cc76f6799a693eb9e977b5e332db43abe01f'
            'ab7f65e4af1a52d899ba01c3042a1b49a29dec976dae93f17f8f6c4067991c04'
            '90a797525021a6a55e16d15f962ade80f7f1f84aab00ce6b577f539c42fb1029'
            '78d26c08c234594eeba0ac68c95612a8c01ea4026f34e0141e8a997287b0af1b')

prepare() {
    # Extract app from installer
    7z x -so $pkgname-$pkgver-setup.exe "\$PLUGINSDIR/app-32.7z" >app-32.7z
    # Extract app archive
    7z x -y -bsp0 -bso0 app-32.7z

    # Extract png from ico container
    magick resources/win/app.ico resources/win/deezer-%d.png

    cd resources/
    asar extract app.asar app

    cd "$srcdir/resources/app"
    mkdir -p resources/linux/
    for size in 24 48; do
	    magick "$srcdir/resources/win/deezer-8.png" -resize "${size}x${size}" -strip \
            -define png:compression-filter=5 -define png:compression-level=9 \
            "resources/linux/systray-${size}.png"
    done

    prettier --write "build/*.js"

    local src
    for src in "${source[@]}"; do
      src="${src%%::*}"
      src="${src##*/}"
      [[ $src = *.patch ]] || continue
      echo "Applying patch ${src}..."
      patch -Np1 -l -F3 < "${srcdir}/${src}"
    done

    cd "$srcdir/resources/"
    asar pack app app.asar
}

package() {
    mkdir -p "$pkgdir/usr/share/deezer"
    mkdir -p "$pkgdir/usr/share/applications"
    mkdir -p "$pkgdir/usr/bin/"
    for size in 16 22 24 32 48 64 128 256 512; do
        install -d "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/"
        magick resources/win/deezer-8.png -resize "${size}x${size}" -strip \
            -define png:compression-filter=5 -define png:compression-level=9 \
            "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/deezer.png"
    done

    install -Dm644 resources/app.asar "$pkgdir/usr/share/deezer/"
    install -Dm644 "$pkgname.desktop" "$pkgdir/usr/share/applications/"
    install -Dm755 deezer "$pkgdir/usr/bin/"
}
