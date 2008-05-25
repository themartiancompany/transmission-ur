# Maintainer: Roman Kyrylych <roman@archlinux.org>
# Contributor: Alessio 'mOLOk' Bolognino <themolok@gmail.com>

pkgname=transmission
pkgver=1.21
pkgrel=1
pkgdesc="A free, lightweight BitTorrent client with an intuitive interface"
arch=('i686' 'x86_64')
url="http://transmission.m0k.org"
license=('MIT')
makedepends=('gtk2')
depends=('openssl' 'curl')
makedepends=('gtk2' 'libnotify')
backup=(etc/conf.d/transmissiond)
install=transmission.install
source=(http://download.m0k.org/transmission/files/transmission-$pkgver.tar.bz2 \
    	transmissiond transmissiond.conf)
md5sums=('6ec565ac831484098a2db20ea0ca3ac7'
         'b18a412502ac51207c79a4aa4066a686'
         '7b019a710493ec4f8b54a1f528e1981d')

build() {
  cd $startdir/src/transmission-$pkgver
  ./configure --prefix=/usr
  make || return 1
  make DESTDIR=${startdir}/pkg install || return 1

  install -m 755 -D $startdir/src/transmissiond \
    $startdir/pkg/etc/rc.d/transmissiond

  install -m 644 -D $startdir/src/transmissiond.conf \
    $startdir/pkg/etc/conf.d/transmissiond

  install -m 644 -D LICENSE $startdir/pkg/usr/share/licenses/$pkgname/LICENSE
}
