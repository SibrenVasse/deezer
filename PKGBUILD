# Maintainer: Sibren Vasse <arch@sibrenvasse.nl>
# Contributor: Ilya Gulya <ilyagulya@gmail.com>
pkgname="deezer2"
pkgver=5.30.60
pkgrel=1
pkgdesc="A proprietary music streaming service"
arch=('any')
url="https://www.deezer.com/"
license=('custom:"Copyright (c) 2006-2018 Deezer S.A."')
depends=('electron13')
provides=('deezer')
makedepends=('p7zip' 'asar' 'prettier' 'imagemagick' 'npm' 'nodejs')
source=("$pkgname-$pkgver-setup.exe::https://www.deezer.com/desktop/download/artifact/win32/x86/$pkgver"
    "$pkgname.desktop"
    "$pkgname"
    start-hidden-on-tray.patch
    quit.patch
    instance.patch)
sha256sums=('026cc41022aee5f3ee97e3b5a61a535ecfce4c19ba9d04c4e5ac6712268c3951'
            'a232ec0febbcf5572e3622c264616877b83f9397a4335e225136cec74a55cf3c'
            'cd5e0cad607bcfdbf44bc5c3bac0af80b826380414613b5bad464fecc14fc203'
            '2254632a03ca2cf7ae6b50a4109b0bec417cf0db6d669a8037125d13488e3b9f'
            'd3f96ae6019abb60aa097919b22b1873f83061ed7453cd251e43b3afe5d54919'
            '29c4b93dbf0d0915ac54773d62bae86910ec5d2dc3e8aaf39244044419845d41')

prepare() {
    # Extract app from installer
    7z x -so $pkgname-$pkgver-setup.exe "\$PLUGINSDIR/app-32.7z" >app-32.7z
    # Extract app archive
    7z x -y -bsp0 -bso0 app-32.7z

    # Extract png from ico container
    convert resources/win/app.ico resources/win/deezer.png

    cd resources/
    asar extract app.asar app

    cd "$srcdir/resources/app"
    mkdir -p resources/linux/
    install -Dm644 "$srcdir/resources/win/systray.png" resources/linux/

    prettier --write "build/*.js"
    # Hide to tray (https://github.com/SibrenVasse/deezer/issues/4)
    patch --forward --strip=1 --input="$srcdir/quit.patch"
    # Add start in tray cli option (https://github.com/SibrenVasse/deezer/pull/12)
    patch --forward --strip=1 --input="$srcdir/start-hidden-on-tray.patch"
    # Run as secondary instance (https://github.com/SibrenVasse/deezer/issues/14)
    patch --forward --strip=1 --input="$srcdir/instance.patch"

    cd "$srcdir/resources/"
    asar pack app app.asar
}

package() {
    mkdir -p "$pkgdir/usr/share/deezer2/"
    mkdir -p "$pkgdir/usr/share/applications/"
    mkdir -p "$pkgdir/usr/bin/"

    install -Dm644 resources/app.asar "$pkgdir/usr/share/deezer2/"
    install -Dm644 "$pkgname.desktop" "$pkgdir/usr/share/applications/"
    install -Dm755 "$pkgname" "$pkgdir/usr/bin/"
}
