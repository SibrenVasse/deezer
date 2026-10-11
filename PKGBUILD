# Maintainer: Sibren Vasse <arch@sibrenvasse.nl>
# Contributor: Ilya Gulya <ilyagulya@gmail.com>

pkgname="deezer"
pkgver=8.0.0
pkgrel=1

# Keep this in sync with the formatter version used by aunetx/deezer-linux.
# The upstream patches are easier to apply when the extracted Deezer JS/HTML
# has been formatted with the same Prettier version.
_prettier_ver=3.5.3

# Commit hash for aunetx/deezer-linux patches.
# Pinning the commit keeps the patch contents reproducible.
_patch_commit=95b02d57381c4e55fedb0bf14066ff9edd7962ee

pkgdesc="A proprietary music streaming service"
arch=('any')
url="https://www.deezer.com/"
license=('LicenseRef-Deezer')
depends=('electron44' 'hicolor-icon-theme' 'xdg-utils')
provides=('deezer')
makedepends=('7zip' 'asar' 'imagemagick' 'nodejs')

source=(
    "$pkgname-$pkgver-setup.exe::https://www.deezer.com/desktop/download/artifact-win32-x86-$pkgver"
    "$pkgname-desktop.desktop"
    "deezer-desktop"
    "prettierrc.json"
    "LICENSE"

    # Download the exact Prettier tarball
    "prettier-${_prettier_ver}.tgz::https://registry.npmjs.org/prettier/-/prettier-${_prettier_ver}.tgz"

    # Upstream and local patches for Deezer 8.0.0.
    "01-start-in-tray-${_patch_commit}.patch::https://raw.githubusercontent.com/aunetx/deezer-linux/${_patch_commit}/patches/01-start-in-tray.patch"
    "02-start-without-tray.patch"
    "03-disable-auto-updater-${_patch_commit}.patch::https://raw.githubusercontent.com/aunetx/deezer-linux/${_patch_commit}/patches/03-disable-auto-updater.patch"
    "04-remove-os-information.patch"
    "06-control-log-level-${_patch_commit}.patch::https://raw.githubusercontent.com/aunetx/deezer-linux/${_patch_commit}/patches/06-control-log-level.patch"
    "07-improve-responsiveness.patch"
    "08-hide-offline-banner.patch"
    "09-disable-animations.patch"
    "10-disable-notifications.patch"
    "12-adjust-volume-on-wheel.patch"
    "13-disable-hardware-acceleration.patch"
    "15-sync-theme.patch"

    # Local patches
    "99-systray-icon.patch"
)

noextract=(
    "prettier-${_prettier_ver}.tgz"
)

sha256sums=('a45091129694c0ed92fa7534b1742c683e869c462766d9fa4b4193ee57015f95'
            'c33b398d8ae279a620cf2914c806f7ee00c47519e4f404874cfd90a68fc8d80f'
            'addc90b0950c9432fe4fd9e99540cedd778de531413403668cd583a91a19d5c6'
            '951c80c9dc4848b31a5fcc9655b005f3a218b1ff735396168c13922ac658c83b'
            'fe9bab50ef433516105ed2f2c349736c0be87c2f75f8d18bf1f011b2115019c1'
            '14841de7b71a93123d22997db22d088debb13976778d869e3622199108e85b4b'
            '655681a094721af8fba69b729b8276ee345e5aac99239726c3e53039b0811cef'
            'c00afc2542c8a58e0d7c9f3c36f513a23b10866246ea543076f3388e71888ac2'
            'f07df06e56552116ad15bb8ee1c01dede0947dcfeb763a0ddeb8d87732c37f40'
            '9f846bbf9c5281175150f3b7ecbcc2a027c6f4dbb4abed307c6fc39d6b19e170'
            'a88886df73b38a23fd8fac931cad86f7b7d6fb4b51697ba236311869ab09180d'
            '83177123ca4157887ac74d88853b2d1e5fe05c0556fdea5e30ec919ab3653db6'
            '379155a0bcb373c05797d204bffb5f8d87b65e918c5dc843b7472885b701731a'
            '310b90a12b444289957ad660e2847a5d5f41c30a4ae1a71c74553613aa4df4b3'
            '6b60356ddc47f6df04d5e449dd72a143a837ca64816d7bcd34c509bf746db0d2'
            '87e37bf204507e1cca42be071140ecc0365c156f81e306b7a9eae796f1ab4693'
            '6f73e43b0b26ccceb6ef6a9d0d7a625448898e22bea72ce43d5cf23bc766f547'
            'ece172c579a08c08ef8503c5fc4c158dcaa2e5b8b313937eeb9e02f552ab8c9d'
            '2362c52147cf67759388ba6685e2add32c5bcfcdbdbca8e0990f58cafa0392de')

_setup_prettier() {
    # Extract the prettier tarball into a stable location.
    rm -rf "$srcdir/prettier"
    mkdir -p "$srcdir/prettier"

    bsdtar -xzf "$srcdir/prettier-${_prettier_ver}.tgz" \
        -C "$srcdir/prettier" \
        --strip-components=1
}

_prettier() {
    # Wrapper around the pinned Prettier CLI.
    node "$srcdir/prettier/bin/prettier.cjs" "$@"
}

prepare() {
    # Extract the Electron app payload from Deezer's Windows installer.
    7z x -so "$pkgname-$pkgver-setup.exe" "\$PLUGINSDIR/app-32.7z" > app-32.7z

    # Extract the app archive so resources/app.asar can be modified.
    7z x -y -bsp0 -bso0 app-32.7z

    # Extract PNG icons from Deezer's Windows .ico file.
    magick resources/win/app.ico resources/win/deezer-%d.png

    # Set up the exact Prettier version used to normalize Deezer's JS/HTML
    # before applying aunetx/deezer-linux patches.
    _setup_prettier

    cd resources/

    # Unpack Electron's asar bundle so we can patch the app contents.
    asar extract app.asar app

    cd "$srcdir/resources/app"
    mkdir -p resources/linux/

    # Generate small tray icons.
    for size in 24 48; do
        magick "$srcdir/resources/win/deezer-8.png" -resize "${size}x${size}" -strip \
            -define png:compression-filter=5 -define png:compression-level=9 \
            "resources/linux/systray-${size}.png"
    done

    # Use the project-specific Prettier config so the generated formatting
    # matches what the upstream patches expect.
    cp "$srcdir/prettierrc.json" .prettierrc
    _prettier --write "build/*.{js,html}"

    # Apply every patch listed in source=().
    local src
    for src in "${source[@]}"; do
        src="${src%%::*}"
        src="${src##*/}"

        [[ $src = *.patch ]] || continue

        echo "Applying patch ${src}..."
        patch --batch --forward --no-backup-if-mismatch -Np1 -l -F0 < "${srcdir}/${src}"
    done

    cd "$srcdir/resources/"

    # Repack the modified Electron app.
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
            "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/deezer-desktop.png"

        chmod 644 "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/deezer-desktop.png"
    done

    install -Dm644 resources/app.asar "$pkgdir/usr/share/deezer/app.asar"
    install -Dm644 "$pkgname-desktop.desktop" "$pkgdir/usr/share/applications/$pkgname-desktop.desktop"
    install -Dm755 deezer-desktop "$pkgdir/usr/bin/deezer-desktop"
    install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
