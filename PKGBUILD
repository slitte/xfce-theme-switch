# Maintainer: slitte <slitte@users.noreply.github.com>
pkgname=xfce-theme-switch
pkgver=1.0.2
pkgrel=1
pkgdesc="Automatic dark/light theme switcher for XFCE based on sun position, with system tray"
arch=('any')
url="https://github.com/slitte/xfce-theme-switch"
license=('MPL-2.0')
depends=(
    'python>=3.9'
    'python-gobject'
    'libappindicator3'
    'xfce4-settings'
    'glib2'
    'procps-ng'
)
optdepends=(
    'matcha-gtk-theme: default theme (dark/light)'
    'papirus-icon-theme: default icon theme'
    'qt5ct: Qt5 theme support'
    'qt6ct: Qt6 theme support'
)
source=("$pkgname-$pkgver.tar.gz::$url/archive/v$pkgver.tar.gz")
sha256sums=('08b6074a6af49d6eb1e705a7b29f4723194878c5d1c89afe4f5f57308f51fce7')

package() {
    cd "$srcdir/$pkgname-$pkgver"

    install -Dm755 bin/xfce-theme-switch "$pkgdir/usr/bin/xfce-theme-switch"
    install -Dm755 bin/xfce-theme-tray   "$pkgdir/usr/bin/xfce-theme-tray"

    install -Dm644 systemd/xfce-theme-switch.service \
        "$pkgdir/usr/lib/systemd/user/xfce-theme-switch.service"
    install -Dm644 systemd/xfce-theme-switch.timer \
        "$pkgdir/usr/lib/systemd/user/xfce-theme-switch.timer"
    install -Dm644 systemd/xfce-theme-tray.service \
        "$pkgdir/usr/lib/systemd/user/xfce-theme-tray.service"

    install -Dm644 LICENSE.md \
        "$pkgdir/usr/share/licenses/$pkgname/LICENSE.md"
    install -Dm644 README.md \
        "$pkgdir/usr/share/doc/$pkgname/README.md"
}
