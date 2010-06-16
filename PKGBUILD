# Maintainer : Ionut Biru <ibiru@archlinux.org>

pkgbase=transmission
pkgname=('transmission-cli' 'transmission-gtk' 'transmission-qt')
pkgver=2.00
pkgrel=2
arch=('i686' 'x86_64')
url="http://www.transmissionbt.com/"
license=('MIT')
makedepends=('intltool' 'curl' 'libnotify' 'desktop-file-utils' 'qt' 'libevent')
source=(http://mirrors.m0k.org/transmission/files/${pkgbase}-${pkgver}.tar.bz2
        transmissiond transmissiond.conf qtr.desktop)
md5sums=('02a4d23bdd6be2fba6d8e0b24f630dab'
         '50b7c17300bc8b38f44fb6f681ba05d0'
         '4170b71ad5d6c499a8a16c6e86b77cce'
         '18912ee58b00938c177f64f6bdd6eed9')
build() {
  cd "${srcdir}/${pkgbase}-${pkgver}"

  export CFLAGS="$CFLAGS -fno-strict-aliasing"

  ./configure --prefix=/usr || return 1
  make || return 1
  pushd qt
  qmake qtr.pro || return 1
  make || return 1
}
package_transmission-cli() {
  pkgdesc="Fast, easy, and free BitTorrent client (CLI tools, daemon and web client)"
  depends=('curl' 'libevent')
  backup=('etc/conf.d/transmissiond')
  install=transmission-cli.install

  cd "${srcdir}/${pkgbase}-${pkgver}"

  for dir in daemon cli web
  do
      make -C "${dir}" DESTDIR="${pkgdir}" install || return 1
  done

  install -D -m755 "${srcdir}/transmissiond" "${pkgdir}/etc/rc.d/transmissiond" || return 1
  install -D -m644 "${srcdir}/transmissiond.conf" "${pkgdir}/etc/conf.d/transmissiond" || return 1
  install -D -m644 COPYING "${pkgdir}/usr/share/licenses/transmission-cli/COPYING" || return 1
}

package_transmission-gtk() {
  pkgdesc="Fast, easy, and free BitTorrent client (GTK+ GUI)"
  depends=('curl' 'libevent' 'libnotify' 'desktop-file-utils' 'hicolor-icon-theme')
  optdepends=('notification-daemon: Desktop notification support'
  	      'transmission-cli: daemon and web support')
  install=transmission-gtk.install

  cd "${srcdir}/${pkgbase}-${pkgver}"

  make -C gtk DESTDIR="${pkgdir}" install || return 1
  make -C po DESTDIR="${pkgdir}" install || return 1
  install -D -m644 COPYING "${pkgdir}/usr/share/licenses/transmission-gtk/COPYING" || return 1
}

package_transmission-qt() {
  pkgdesc="Fast, easy, and free BitTorrent client (Qt GUI)"
  depends=('curl' 'qt' 'libevent')
  optdepends=('transmission-cli: daemon and web support')

  cd "${srcdir}/${pkgbase}-${pkgver}"

  make -C qt INSTALL_ROOT="${pkgdir}"/usr install || return 1

  install -D -m644 COPYING "${pkgdir}/usr/share/licenses/transmission-qt/COPYING" || return 1
  install -D -m644 gtk/transmission.png "${pkgdir}/usr/share/pixmaps/transmission-qt.png" || return 1
  install -D -m644 "${srcdir}"/qtr.desktop "${pkgdir}/usr/share/applications/qtr.desktop" || return 1
}
