# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Contributor : Tom Gundersen <teg@jklm.no>
# Contributor : Ionut Biru <ibiru@archlinux.org>
# Contributor : Thomas Weißschuh <thomas t-8ch de>
# Contributor : Florian Pritz <bluewind@xinu.at>

_gtk="true"
_qt="true"
_ui="true"
_systemd="true"
_os="$( \
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _libc="libc++"
  _systemd="false"
  _ui="false"
fi
if [[ "${_ui}" == "false" ]]; then
  _gtk="false"
  _qt="false"
fi
_pkg="transmission"
pkgbase="${_pkg}"
pkgname=(
  "${_pkg}-cli"
  "lib${_pkg}"
)
if [[ "${_gtk}" == "true" ]]; then
  pkgname+=(
    "${_pkg}-gtk"
  )
fi
if [[ "${_qt}" == "true" ]]; then
  pkgname+=(
    "${_pkg}-qt"
  )
fi
pkgver=4.0.6
pkgrel=3
arch=(
  'x86_64'
  'i686'
  'arm'
  'armv7l'
  'armv6l'
  'aarch64'
  'mips'
  'powerpc'
  'pentium4'
)
url="http://www.${_pkg}bt.com"
license=(
  "GPL"
)
makedepends=(
  "cmake"
  "curl"
  "dht"
  "intltool"
  "libb64"
  "libdeflate"
  "libevent"
  "libnatpmp"
  "miniupnpc"
  "ninja"
  "npm"
)
if [[ "${_systemd}" == "true" ]]; then
  depends+=(
    "systemd"
  )
fi
if [[ "${_ui}" == "true" ]]; then
  depends+=(
    "libayatana-indicator"
  )
fi
if [[ "${_gtk}" == "true" ]]; then
  depends+=(
    "glibmm-2.68"
    "gtk4"
    "gtkmm-4.0"
  )
fi
if [[ "${_qt}" == "true" ]]; then
  depends+=(
    "qt6-base"
    "qt6-svg"
    "qt6-tools"
  )
fi

_http="https://github.com"
_ns="transmission"
_url="${_http}/${_ns}/${_pkg}"
source=(
  "${_url}/releases/download/${pkgver}/${_pkg}-${pkgver}.tar.xz"
  "febfe49c.patch"
  "${_pkg}-cli.sysusers"
  "${_pkg}-cli.tmpfiles"
)
sha256sums=('2a38fe6d8a23991680b691c277a335f8875bdeca2b97c6b26b598bc9c7b0c45f'
            '1e5917c79a0c17595f18b544c5c1ab101ecbef5b2ffb0ca42a0a3b221a85e044'
            '641310fb0590d40e00bea1b5b9c843953ab78edf019109f276be9c6a7bdaf5b2'
            '1266032bb07e47d6bcdc7dabd74df2557cc466c33bf983a5881316a4cc098451')

prepare() {
  cd $pkgbase-$pkgver
  patch -p1 -i ../febfe49c.patch # Fix build with miniupnpc 2.2.8
}

build() {
  export CFLAGS+=" -ffat-lto-objects"
  cd $pkgbase-$pkgver

  cmake -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo \
	  -DCMAKE_INSTALL_PREFIX=/usr \
	  -DENABLE_CLI=ON \
	  -DENABLE_DAEMON=ON \
	  -DENABLE_GTK=ON \
	  -DENABLE_MAC=OFF \
	  -DENABLE_QT=ON \
	  -DREBUILD_WEB=ON \
	  -DENABLE_TESTS=ON \
	  -DENABLE_UTILS=ON \
	  -DENABLE_UTP=ON \
	  -DINSTALL_LIB=ON \
      -DUSE_SYSTEM_B64=ON \
      -DUSE_SYSTEM_DEFLATE=ON \
      -DUSE_SYSTEM_DHT=ON \
	  -DUSE_SYSTEM_EVENT2=ON \
      -DUSE_SYSTEM_MINIUPNPC=ON \
      -DUSE_SYSTEM_NATPMP=ON \
      -DUSE_SYSTEM_PSL=ON \
      -DUSE_SYSTEM_UTP=OFF \
	  -DWITH_CRYPTO=openssl \
	  -S . -B build

  cmake --build build --config Release
}

check() {
  cd $pkgbase-$pkgver

  cd build
  ctest --output-on-failure -j "$(nproc)"
}

_install_component() {
  (cd $srcdir/$pkgbase-$pkgver/build; DESTDIR="$pkgdir" ninja $1/install)
}

package_transmission-cli() {
  pkgdesc='Fast, easy, and free BitTorrent client (CLI tools, daemon and web client)'
  depends=(curl libevent systemd libb64 miniupnpc libnatpmp libdeflate)

  cd $pkgbase-$pkgver

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
  depends=(curl libevent gtk4 hicolor-icon-theme libayatana-indicator libb64 miniupnpc libnatpmp libdeflate gtkmm-4.0)
  optdepends=('libnotify: Desktop notification support'
              'transmission-cli: daemon and web support')

  cd $pkgbase-$pkgver

  _install_component gtk
  _install_component po

  install -Dm644 COPYING "$pkgdir/usr/share/licenses/transmission-gtk/COPYING"
}

package_transmission-qt() {
  pkgdesc='Fast, easy, and free BitTorrent client (Qt GUI)'
  depends=(curl qt6-base qt6-svg libayatana-indicator libevent libb64 miniupnpc libnatpmp libdeflate)
  optdepends=('transmission-cli: daemon and web support')

  cd $pkgbase-$pkgver

  _install_component qt

  install -Dm644 COPYING "$pkgdir/usr/share/licenses/transmission-qt/COPYING"
}

package_libtransmission() {
  pkgdesc='Fast, easy, and free BitTorrent client (shared library)'

  cd $pkgbase-$pkgver

  install -Dm644 build/libtransmission/libtransmission.a -t "$pkgdir"/usr/lib
  install -Dm644 libtransmission/*.h -t "$pkgdir"/usr/include/transmission
  install -Dm644 COPYING "$pkgdir/usr/share/licenses/libtransmission/COPYING"
}

