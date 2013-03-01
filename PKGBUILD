# Maintainer : Ionut Biru <ibiru@archlinux.org>

pkgbase=transmission
pkgname=('transmission-cli' 'transmission-gtk' 'transmission-qt')
pkgver=2.77
pkgrel=2
arch=('i686' 'x86_64')
url="http://www.transmissionbt.com/"
license=('MIT')
makedepends=('gtk3' 'intltool' 'curl' 'qt4' 'libevent')
source=(http://mirrors.m0k.org/transmission/files/$pkgbase-$pkgver.tar.xz
        transmissiond transmissiond.conf transmission.systemd
        transmission.tmpfiles)
md5sums=('60c0e77a1852656215f7102a437b2a3d'
         '7d6186ee2a852ae3d44980f05063e194'
         'db72b02fee139e8ab416324e6c044d76'
         '5c289c8901221a94be74665368ab5c2c'
         '23f2320361ad54373c3a4551ef634fe8')

build() {
  cd $pkgbase-$pkgver

  ./configure --prefix=/usr
  make
  pushd qt
  qmake-qt4 qtr.pro
  make
}
package_transmission-cli() {
  pkgdesc="Fast, easy, and free BitTorrent client (CLI tools, daemon and web client)"
  depends=('curl' 'libevent')
  backup=('etc/conf.d/transmissiond')
  install=transmission-cli.install

  cd $pkgbase-$pkgver

  for dir in daemon cli web utils
  do
      make -C "$dir" DESTDIR="$pkgdir" install
  done

  install -D -m755 "$srcdir/transmissiond" "$pkgdir/etc/rc.d/transmissiond"
  install -D -m644 "$srcdir/transmissiond.conf" "$pkgdir/etc/conf.d/transmissiond"
  install -D -m644 "$srcdir/transmission.tmpfiles" "$pkgdir/usr/lib/tmpfiles.d/transmission.conf"
  install -D -m644 "$srcdir/transmission.systemd" "$pkgdir/usr/lib/systemd/system/transmission.service"
  install -D -m644 COPYING "$pkgdir/usr/share/licenses/transmission-cli/COPYING"
}

package_transmission-gtk() {
  pkgdesc="Fast, easy, and free BitTorrent client (GTK+ GUI)"
  depends=('curl' 'libevent' 'gtk3' 'desktop-file-utils' 'hicolor-icon-theme')
  optdepends=('notification-daemon: Desktop notification support'
  	      'transmission-cli: daemon and web support')
  install=transmission-gtk.install

  cd $pkgbase-$pkgver

  make -C gtk DESTDIR="$pkgdir" install
  make -C po DESTDIR="$pkgdir" install
  install -D -m644 COPYING "$pkgdir/usr/share/licenses/transmission-gtk/COPYING"
}

package_transmission-qt() {
  pkgdesc="Fast, easy, and free BitTorrent client (Qt GUI)"
  depends=('curl' 'qt4' 'libevent')
  optdepends=('transmission-cli: daemon and web support')
  install=transmission-qt.install

  cd $pkgbase-$pkgver

  make -C qt INSTALL_ROOT="$pkgdir"/usr install

  install -D -m644 COPYING "$pkgdir/usr/share/licenses/transmission-qt/COPYING"
  install -D -m644 qt/icons/transmission.png "$pkgdir/usr/share/pixmaps/transmission-qt.png"
  install -D -m644 qt/transmission-qt.desktop "$pkgdir/usr/share/applications/transmission-qt.desktop"
}
