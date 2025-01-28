# Maintainer: Caleb Maclennan <caleb@alerque.com>
# Contributor: Tom Gundersen <teg@jklm.no>
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Thomas Weißschuh <thomas t-8ch de>
# Contributor: Florian Pritz <bluewind@xinu.at>

pkgbase=transmission
pkgname=(transmission-cli transmission-gtk transmission-qt)
pkgver=4.0.6
pkgrel=8
arch=(x86_64)
url="http://www.transmissionbt.com/"
license=(GPL-2.0-or-later)
makedepends=(cmake
             curl
             dht
             glibmm-2.68
             gtk4
             gtkmm-4.0
             intltool
             libayatana-indicator
             libb64
             libdeflate
             libevent
             libnatpmp
             miniupnpc
             ninja
             npm
             qt6-base
             qt6-svg
             qt6-tools
             systemd)
_archive="$pkgbase-$pkgver"
source=("https://github.com/transmission/transmission/releases/download/$pkgver/$_archive.tar.xz"
        febfe49c.patch
        transmission-cli.sysusers
        transmission-cli.tmpfiles)
sha256sums=('2a38fe6d8a23991680b691c277a335f8875bdeca2b97c6b26b598bc9c7b0c45f'
            '1e5917c79a0c17595f18b544c5c1ab101ecbef5b2ffb0ca42a0a3b221a85e044'
            '641310fb0590d40e00bea1b5b9c843953ab78edf019109f276be9c6a7bdaf5b2'
            '1266032bb07e47d6bcdc7dabd74df2557cc466c33bf983a5881316a4cc098451')

prepare() {
	cd "$_archive"
	patch -p1 -i ../febfe49c.patch # Fix build with miniupnpc 2.2.8
}

build() {
	export CFLAGS+=' -ffat-lto-objects'
	cd "$_archive"

	cmake \
		-G Ninja \
		-D CMAKE_BUILD_TYPE=RelWithDebInfo \
		-D CMAKE_INSTALL_PREFIX=/usr \
		-D ENABLE_CLI=ON \
		-D ENABLE_DAEMON=ON \
		-D ENABLE_GTK=ON \
		-D ENABLE_MAC=OFF \
		-D ENABLE_QT=ON \
		-D REBUILD_WEB=ON \
		-D ENABLE_TESTS=ON \
		-D ENABLE_UTILS=ON \
		-D ENABLE_UTP=ON \
		-D INSTALL_LIB=ON \
		-D USE_SYSTEM_B64=ON \
		-D USE_SYSTEM_DEFLATE=ON \
		-D USE_SYSTEM_DHT=ON \
		-D USE_SYSTEM_EVENT2=ON \
		-D USE_SYSTEM_MINIUPNPC=ON \
		-D USE_SYSTEM_NATPMP=ON \
		-D USE_SYSTEM_PSL=ON \
		-D USE_SYSTEM_UTP=OFF \
		-D WITH_CRYPTO=openssl \
		-S . \
		-B build
	cmake --build build --config Release
}

check() {
	cd "$_archive"
	cd build
	# Exclude test that fails on the build server
	ctest --output-on-failure -j "$(nproc)" -E LT.DhtTest.usesBootstrapFile
}

_install_component() {
	(cd $srcdir/$pkgbase-$pkgver/build; DESTDIR="$pkgdir" ninja $1/install)
}

package_transmission-cli() {
	pkgdesc='Fast, easy, and free BitTorrent client (CLI tools, daemon and web client)'
	depends=(curl
	         libb64
	         libdeflate
	         libevent
	         libnatpmp
	         miniupnpc
	         systemd)
	cd "$_archive"
	for dir in daemon cli web utils; do
		_install_component $dir
	done
	install -d "$pkgdir"/usr/share/transmission
	cp -a build/web/public_html/ "$pkgdir"/usr/share/transmission
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
	depends=(curl
	         gtk4
	         gtkmm-4.0
	         hicolor-icon-theme
	         libayatana-indicator
	         libb64
	         libdeflate
	         libevent
	         libnatpmp
	         miniupnpc)
	optdepends=('libnotify: Desktop notification support'
	            'transmission-cli: daemon and web support')
	cd "$_archive"
	_install_component gtk
	_install_component po
	install -Dm644 COPYING "$pkgdir/usr/share/licenses/transmission-gtk/COPYING"
}

package_transmission-qt() {
	pkgdesc='Fast, easy, and free BitTorrent client (Qt GUI)'
	depends=(curl
	         libb64
	         libdeflate
	         libevent
	         libnatpmp
	         miniupnpc
	         qt6-base
	         qt6-svg)
	optdepends=('transmission-cli: daemon and web support')
	cd "$_archive"
	_install_component qt
	install -Dm644 COPYING "$pkgdir/usr/share/licenses/transmission-qt/COPYING"
}
