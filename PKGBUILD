# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Contributor : Tom Gundersen <teg@jklm.no>
# Contributor : Ionut Biru <ibiru@archlinux.org>
# Contributor : Thomas Weißschuh <thomas t-8ch de>
# Contributor : Florian Pritz <bluewind@xinu.at>

_b64="true"
_gtk="true"
_qt="true"
_ui="true"
_systemd="true"
_remote="true"
_natpmp="libnatpmp"
_nls="auto"
_termux_services="false"
_tests="true"
_os="$( \
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _b64="false"
  _natpmp="${_natpmp}c"
  _nls="false"
  _libc="libc++"
  _systemd="false"
  _termux_services="true"
  _tests="false"
  _ui="false"
fi
if [[ "${_ui}" == "false" ]]; then
  _gtk="false"
  _qt="false"
fi
_pkg=transmission
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
  "libpsl"
  "${_natpmp}"
  "miniupnpc"
  "ninja"
  "openssl>=3"
)
if [[ "${_remote}" == "true" ]]; then
  makedepends+=(
    "npm"
  )
fi
if [[ "${_systemd}" == "true" ]]; then
  makedepends+=(
    "systemd"
  )
fi
if [[ "${_ui}" == "true" ]]; then
  makedepends+=(
    "libayatana-indicator"
  )
fi
if [[ "${_gtk}" == "true" ]]; then
  makedepends+=(
    "glibmm-2.68"
    "gtk4"
    "gtkmm-4.0"
  )
fi
if [[ "${_qt}" == "true" ]]; then
  makedepends+=(
    "qt6-base"
    "qt6-svg"
    "qt6-tools"
  )
fi
_tarname="${_pkg}-${pkgver}"
_http="https://github.com"
_ns="transmission"
_url="${_http}/${_ns}/${_pkg}"
source=(
  "${_url}/releases/download/${pkgver}/${_tarname}.tar.xz"
  "0001-miniupnpc-2.2.8-fix.patch"
  "${_pkg}-cli.sysusers"
  "${_pkg}-cli.tmpfiles"
)
sha256sums=(
  '2a38fe6d8a23991680b691c277a335f8875bdeca2b97c6b26b598bc9c7b0c45f'
  '1e5917c79a0c17595f18b544c5c1ab101ecbef5b2ffb0ca42a0a3b221a85e044'
  '641310fb0590d40e00bea1b5b9c843953ab78edf019109f276be9c6a7bdaf5b2'
  '1266032bb07e47d6bcdc7dabd74df2557cc466c33bf983a5881316a4cc098451'
)
if [[ "${_os}" == "Android" ]]; then
  source+=(
    "0002-no-quota.patch"
    "0003-fix-paths.patch"
  )
  sha256sums+=(
    "7df2182a741e957d5efd9c941fccf6ab6812ab3cf2542e125374cb0a5fbd5f3c"
    "75e880d1642a2fa30a5f5e6a6efd6370ad1ea64faecbe1f52548e85544405eed"
  )
fi

prepare() {
  cd \
    "${_tarname}"
  patch \
    -p1 \
    -i \
    "../0001-miniupnpc-2.2.8-fix.patch"
  if [[ "${_os}" == "Android" ]]; then
    patch \
      -p1 \
      -i \
      "../0002-no-quota.patch"
    patch \
      -p1 \
      -i \
      "../0003-fix-paths.patch"
  fi
}

_usr_get() {
  local \
    _bin
  _bin="$( \
    dirname \
      "$(command \
           -v \
	   "clang" \
	   "cxx" \
	   "g++" | \
	   head \
	     -n \
	     1)")"
  echo \
    "$(dirname \
         "${_bin}")"
}

build() {
  local \
    _cmake_opts=() \
    _b64_opt \
    _gtk_opt \
    _tests_opt \
    _qt_opt \
    _web_opt
  if [[ "${_b64}" == "true" ]]; then
    _b64_opt="ON"
  elif [[ "${_b64}" == "false" ]]; then
    _b64_opt="OFF"
  fi
  if [[ "${_gtk}" == "true" ]]; then
    _gtk_opt="ON"
  elif [[ "${_gtk}" == "false" ]]; then
    _gtk_opt="OFF"
  fi
  if [[ "${_qt}" == "true" ]]; then
    _qt_opt="ON"
  elif [[ "${_qt}" == "false" ]]; then
    _qt_opt="OFF"
  fi
  if [[ "${_remote}" == "true" ]]; then
    _web_opt="ON"
  elif [[ "${_remote}" == "false" ]]; then
    _web_opt="OFF"
  fi
  if [[ "${_tests}" == "true" ]]; then
    _tests_opt="ON"
  elif [[ "${_tests}" == "false" ]]; then
    _tests_opt="OFF"
  fi
  _cmake_opts+=(
    -DCMAKE_BUILD_TYPE="RelWithDebInfo"
    -DCMAKE_INSTALL_PREFIX="/usr"
    -DENABLE_CLI="ON"
    -DENABLE_DAEMON="ON"
    -DENABLE_GTK="${_gtk_opt}"
    -DENABLE_MAC="OFF"
    -DENABLE_QT="${_qt_opt}"
    -DREBUILD_WEB="${_web_opt}"
    -DENABLE_TESTS="${_tests_opt}"
    -DENABLE_UTILS="ON"
    -DENABLE_UTP="ON"
    -DINSTALL_LIB="ON"
    -DUSE_SYSTEM_B64="${_b64_opt}"
    -DUSE_SYSTEM_DEFLATE="ON"
    -DUSE_SYSTEM_DHT="ON"
    -DUSE_SYSTEM_EVENT2="ON"
    -DUSE_SYSTEM_MINIUPNPC="ON"
    -DUSE_SYSTEM_NATPMP="ON"
    -DUSE_SYSTEM_PSL="ON"
    -DUSE_SYSTEM_UTP="OFF"
    -DWITH_CRYPTO="openssl"
  )
  if [[ "${_os}" == "Android" ]]; then
    _cmake_opts+=(
      -DCMAKE_CXX_STANDARD_INCLUDE_DIRECTORIES="$(_usr_get)/include/c++/v1"
      -DCMAKE_CXX_FLAGS="-Wno-sign-compare -Wno-error-sign-compare"
    )
  fi
  if [[ "${_nls}" == "false" ]]; then
    _cmake_opts+=(
      -DENABLE_NLS="OFF"
    )
  fi
  # export \
  #   CFLAGS+=" -ffat-lto-objects"
  cd \
    "${_tarname}"
  cmake \
    -G \
      Ninja \
    -S \
      "$(pwd)" \
    -B \
      "build" \
    "${_cmake_opts[@]}"
  cmake \
    --build \
      "build" \
    --config \
      "Release"
}

check() {
  cd \
    "${_tarname}/build"
  ctest \
    --output-on-failure \
    -j \
    "$(nproc)"
}

_component_install() {
  local \
    _dir="${1}"
  cd \
    "${srcdir}/${_tarname}/build"
  DESTDIR="${pkgdir}" \
  ninja \
    "${_dir}/install"
}

_termux_service_install() {
  local \
    _service_dir \
    _service_log \
    _service_run \
    _usr \
    _var
  if [[ "${_os}" == "Android" ]]; then
    _usr="${PREFIX}"
    _var="usr/var"
  else
    _usr="/data/data/com.termux/files/usr"
    _var="var"
  fi
  _service_dir="${_var}/service/${_pkg}"
  _service_run=(
    "#!${_usr}/bin/sh"
    "exec transmission-daemon -f 2>&1"
  )
  _service_log=(
    "#!${_usr}/bin/sh"
    'mkdir -p "$LOGDIR/sv/transmission"'
    'exec svlogd "$LOGDIR/sv/transmission"'
  )
  printf \
    "%s\n" \
    "${_service_run[@]}" > \
    "${pkgdir}/${_service_dir}/run"
  chmod \
    755 \
    "${pkgdir}/${_service_dir}/run"
  printf \
    "%s\n" \
    "${_service_log[@]}" > \
    "${pkgdir}/${_service_dir}/log/run"
  chmod \
    755 \
    "${pkgdir}/${_service_dir}/log/run"
}

package_transmission-cli() {
  local \
    _dir \
    _service_run \
    _usr
  _pkgdesc=(
    'Fast, easy, and free'
    'BitTorrent client'
    '(CLI tools, daemon and'
    'web client)'
  )
  pkgdesc="${_pkgdesc[*]}"
  depends=(
    'curl'
    'libdeflate'
    'libevent'
    'libb64'
    'libnatpmp'
    'miniupnpc'
  )
  if [[ "${_os}" == "Android" ]]; then
    depends+=(
      "${_libc}"
    )
  fi
  if [[ "${_systemd}" == "true" ]]; then
    depends+=(
      'systemd'
    )
  fi
  provides=(
    "${_pkg}=${pkgver}"
  )
  cd \
    "${_tarname}"
  for _dir in "daemon" "cli" "web" "utils"; do
    _component_install \
      "${_dir}"
  done
  install \
    -dm755 \
    "${pkgdir}/usr/share/${_pkg}"
  if [[ "${_remote}" == "true" ]]; then
    cp \
      -a \
      "build/web/public_html/" \
      "${pkgdir}/usr/share/${_pkg}"
  fi
  if [[ "${_systemd}" == "true" ]]; then
    install \
      -Dm644 \
      "daemon/${_pkg}-daemon.service" \
      "${pkgdir}/usr/lib/systemd/system/${_pkg}.service"
  fi
  if [[ "${_termux_services}" == "true" ]]; then
    _termux_service_install
  fi
  install \
    -Dm644 \
    "COPYING" \
    "${pkgdir}/usr/share/licenses/${_pkg}-cli/COPYING"
  install \
    -Dm644 \
    "${srcdir}/${_pkg}-cli.sysusers" \
    "${pkgdir}/usr/lib/sysusers.d/${_pkg}.conf"
  install \
    -Dm644 \
    "${srcdir}/${pkg}-cli.tmpfiles" \
    "${pkgdir}/usr/lib/tmpfiles.d/${_pkg}.conf"
}

package_transmission-gtk() {
  _pkgdesc=(
    'Fast, easy, and free'
    'BitTorrent client (GTK+ GUI)'
  )
  pkgdesc="${_pkgdesc[*]}"
  depends=(
    'curl'
    'gtk4'
    'gtkmm-4.0'
    'hicolor-icon-theme'
    'libayatana-indicator'
    'libb64'
    'libdeflate'
    'libevent'
    'libnatpmp'
    'miniupnpc'
  )
  optdepends=(
    'libnotify: Desktop notification support'
    'transmission-cli: daemon and web support'
  )
  provides=(
    "${_pkg}=${pkgver}"
  )
  cd \
    "${_tarname}"
  _component_install \
    "gtk"
  _component_install \
    "po"
  install \
    -Dm644 \
    "COPYING" \
    "${pkgdir}/usr/share/licenses/${_pkg}-gtk/COPYING"
}

package_transmission-qt() {
  _pkgdesc=(
    'Fast, easy, and free'
    'BitTorrent client (Qt GUI)'
  )
  pkgdesc="${_pkgdesc[*]}"
  depends=(
    'curl'
    'libayatana-indicator'
    'libb64'
    'libdeflate'
    'libevent'
    'miniupnpc'
    'libnatpmp'
    'qt6-base'
    'qt6-svg'
  )
  optdepends=(
    'transmission-cli: daemon and web support'
  )
  provides=(
    "${_pkg}=${pkgver}"
  )
  cd \
    "${_tarname}"
  _component_install \
    "qt"
  install \
    -Dm644 \
    "COPYING" \
    "${pkgdir}/usr/share/licenses/${_pkg}-qt/COPYING"
}

package_libtransmission() {
  _pkgdesc=(
    'Fast, easy, and free'
    'BitTorrent client'
    '(shared library)'
  )
  pkgdesc="${_pkgdesc[*]}"
  cd \
    "${_tarname}"
  install \
    -Dm644 \
    "build/lib${_pkg}/lib${_pkg}.a" \
    -t \
    "${pkgdir}/usr/lib"
  install \
    -Dm644 \
    "lib${_pkg}/"*".h" \
    -t \
    "${pkgdir}/usr/include/${_pkg}"
  install \
    -Dm644 \
    "COPYING" \
    "${pkgdir}/usr/share/licenses/lib${_pkg}/COPYING"
}
