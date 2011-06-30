# Maintainer : Ionut Biru <ibiru@archlinux.org>

pkgbase=transmission
pkgname=('transmission-cli' 'transmission-gtk' 'transmission-qt')
pkgver=2.32
pkgrel=1
arch=('i686' 'x86_64')
url="http://www.transmissionbt.com/"
license=('MIT')
makedepends=('gtk2' 'intltool' 'curl' 'libnotify' 'qt' 'libevent' 'libcanberra' 'dbus-glib')
source=(http://mirrors.m0k.org/transmission/files/${pkgbase}-${pkgver}.tar.bz2
        transmissiond transmissiond.conf)
md5sums=('cac5d0e07b92e563b34b61de5a6235c7'
         '08875299e3fbb68fc546c1f350ac1f06'
         'be39806c35b7544856fa4070b00fc960')
build() {
  cd "${srcdir}/${pkgbase}-${pkgver}"

  export CFLAGS="$CFLAGS -fno-strict-aliasing"

  ./configure --prefix=/usr --disable-gconf2
  make
  pushd qt
  qmake qtr.pro
  make
}
package_transmission-cli() {
  pkgdesc="Fast, easy, and free BitTorrent client (CLI tools, daemon and web client)"
  depends=('curl' 'libevent')
  backup=('etc/conf.d/transmissiond')
  install=transmission-cli.install

  cd "${srcdir}/${pkgbase}-${pkgver}"

  for dir in daemon cli web utils
  do
      make -C "${dir}" DESTDIR="${pkgdir}" install
  done

  install -D -m755 "${srcdir}/transmissiond" "${pkgdir}/etc/rc.d/transmissiond"
  install -D -m644 "${srcdir}/transmissiond.conf" "${pkgdir}/etc/conf.d/transmissiond"
  install -D -m644 COPYING "${pkgdir}/usr/share/licenses/transmission-cli/COPYING"
}

package_transmission-gtk() {
  pkgdesc="Fast, easy, and free BitTorrent client (GTK+ GUI)"
  depends=('curl' 'libevent' 'libnotify' 'desktop-file-utils' 'hicolor-icon-theme'
           'gtk2' 'dbus-glib' 'libcanberra')
  optdepends=('notification-daemon: Desktop notification support'
  	      'transmission-cli: daemon and web support')
  install=transmission-gtk.install

  cd "${srcdir}/${pkgbase}-${pkgver}"

  make -C gtk DESTDIR="${pkgdir}" install
  make -C po DESTDIR="${pkgdir}" install
  install -D -m644 COPYING "${pkgdir}/usr/share/licenses/transmission-gtk/COPYING"
}

package_transmission-qt() {
  pkgdesc="Fast, easy, and free BitTorrent client (Qt GUI)"
  depends=('curl' 'qt' 'libevent')
  optdepends=('transmission-cli: daemon and web support')
  install=transmission-qt.install

  cd "${srcdir}/${pkgbase}-${pkgver}"

  make -C qt INSTALL_ROOT="${pkgdir}"/usr install

  install -D -m644 COPYING "${pkgdir}/usr/share/licenses/transmission-qt/COPYING"
  install -D -m644 qt/icons/transmission.png "${pkgdir}/usr/share/pixmaps/transmission-qt.png"
  install -D -m644 qt/transmission-qt.desktop "${pkgdir}/usr/share/applications/transmission-qt.desktop"
}
