# Maintainer : Tom Gundersen <teg@jklm.no>
# Maintainer : Ionut Biru <ibiru@archlinux.org>

pkgbase=transmission
pkgname=('transmission-cli' 'transmission-gtk' 'transmission-qt')
pkgver=2.92
pkgrel=4
arch=('i686' 'x86_64')
url="http://www.transmissionbt.com/"
license=('MIT')
makedepends=('gtk3' 'intltool' 'curl' 'qt5-base' 'libevent' 'systemd'
             'qt5-tools')
source=(https://download.transmissionbt.com/files/transmission-${pkgver}.tar.xz
        transmission-2.90-libsystemd.patch
        transmission-cli.sysusers
        transmission-cli.tmpfiles)
md5sums=('3fce404a436e3cd7fde80fb6ed61c264'
         'bcb54fdb9fec00992960d9bd3b449d4d'
         '6836ecf77e8c96738259e61b4ec0813e'
         '73f17e39f3996768bb1d1e25a2f3fed1')

prepare() {
  cd $pkgbase-$pkgver
  patch -p1 -i "$srcdir/transmission-2.90-libsystemd.patch"
  rm -f m4/glib-gettext.m4
  autoreconf -fi

  sed -i '/^Icon=/ s/$/-qt/' qt/transmission-qt.desktop
}

build() {
  cd $pkgbase-$pkgver
  ./configure --prefix=/usr
  make

  cd qt
  DEFINES+=TRANSLATIONS_DIR=\\\\\\\"/usr/share/transmission-qt/translations\\\\\\\" \
    qmake qtr.pro
  make
  lrelease translations/*.ts
}

package_transmission-cli() {
  pkgdesc='Fast, easy, and free BitTorrent client (CLI tools, daemon and web client)'
  depends=('curl' 'libevent' 'systemd')
  install=transmission-cli.install

  cd $pkgbase-$pkgver

  for dir in daemon cli web utils; do
    make -C "$dir" DESTDIR="$pkgdir" install
  done

  install -Dm644 daemon/transmission-daemon.service \
    "$pkgdir/usr/lib/systemd/system/transmission.service"
  install -Dm644 COPYING "$pkgdir/usr/share/licenses/transmission-cli/COPYING"

  install -Dm644 "$srcdir/$pkgname.sysusers" \
    "$pkgdir/usr/lib/sysusers.d/transmission.conf"
  install -Dm644 "$srcdir/$pkgname.tmpfiles" \
    "$pkgdir/usr/lib/tmpfiles.d/transmission.conf"
}

package_transmission-gtk() {
  pkgdesc='Fast, easy, and free BitTorrent client (GTK+ GUI)'
  depends=('curl' 'libevent' 'gtk3' 'desktop-file-utils' 'hicolor-icon-theme')
  optdepends=('notification-daemon: Desktop notification support'
              'transmission-cli: daemon and web support')

  cd $pkgbase-$pkgver

  make -C gtk DESTDIR="$pkgdir" install
  make -C po DESTDIR="$pkgdir" install
  install -Dm644 COPYING "$pkgdir/usr/share/licenses/transmission-gtk/COPYING"
}

package_transmission-qt() {
  pkgdesc='Fast, easy, and free BitTorrent client (Qt GUI)'
  depends=('curl' 'qt5-base' 'libevent')
  optdepends=('transmission-cli: daemon and web support')

  cd $pkgbase-$pkgver

  make -C qt INSTALL_ROOT="$pkgdir"/usr install
  install -Dm644 -t "$pkgdir/usr/share/transmission-qt/translations" \
    qt/translations/*.qm

  install -Dm644 COPYING "$pkgdir/usr/share/licenses/transmission-qt/COPYING"
  install -Dm644 qt/icons/transmission.png \
    "$pkgdir/usr/share/pixmaps/transmission-qt.png"
  install -Dm644 qt/transmission-qt.desktop \
    "$pkgdir/usr/share/applications/transmission-qt.desktop"
}
