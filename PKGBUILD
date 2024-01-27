# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer:  David Runge <dvzrv@archlinux.org>
# Contributor: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Contributor: Truocolo <truocolo@aol.com>

_os="$( \
  uname \
    -o)"
_pkg=maturin
_py="python"
pkgbase="${_pkg}"
pkgname=(
  "${pkgbase}"
  "${_py}-${_pkg}"
)
pkgver=1.4.0
pkgrel=1
_pkgdesc=(
  "Build and publish crates with pyo3,"
  "rust-c${_py} and cffi bindings"
)
pkgdesc="${_pkgdesc[*]}"
url="https://github.com/PyO3/${_pkg}"
arch=(
  x86_64
  arm
)
license=(
  'Apache-2.0 OR MIT'
)
makedepends=(
  bzip2
  gcc-libs
  git
  glibc
  "${_py}-build"
  "${_py}-installer"
  "${_py}-setuptools"
  "${_py}-setuptools-rust"
  "${_py}-wheel"
  rust
)
# disable LTO until ring can be built with it:
# https://github.com/briansmith/ring/issues/1444
options=(
  !lto
)
source=(
  $url/archive/v$pkgver/$pkgname-v$pkgver.tar.gz
)
sha512sums=(
  'c86afdeb694c2fe4b9b94a7bf5f657f9337d3b6179a3d430a26608353d4de7c463a3860e87225356cfe66f1111240717851173403c66a8c30ed3acf7c1d2afdb'
)
b2sums=(
  'f7b03ab106b180a6b274ddee112e85197abb7518e5cc741afd653cdecbaad3ceb2225218c8f58ee45d562c168a51b69174505cef9d0b1d69cf3aed981ba7d892'
)

_pick() {
  local \
    p="$1" \
    f \
    d; shift
  for f; do
    d="$srcdir/$p/${f#$pkgdir/}"
    mkdir \
      -p \
        "$(dirname "$d")"
    mv \
      "$f" \
      "$d"
    rmdir \
    -p \
    --ignore-fail-on-non-empty \
    "$( \
      dirname \
        "$f")"
  done
}

prepare() {
  local \
    _target
  _target="${CARCH}-unknown-linux-gnu"
  [[ "${_os}" == "Android" ]] && \
    _target="${CARCH}-linux-androideabi"
  cd \
    "${pkgbase}-${pkgver}"
  cargo \
    fetch \
    --locked \
    --target \
      "${_target}"
}

build() {
  cd "${pkgbase}-${pkgver}"
  "${_py}" \
    -m \
      build \
      --wheel \
      --no-isolation
}

package_maturin() {
  depends=(
    bzip2
    gcc-libs
    glibc
    rust
  )
  cd \
    $pkgbase-$pkgver
  "${_py}" \
    -m installer \
    --destdir="$pkgdir" \
    dist/*.whl
  install \
    -vDm 644 \
    {Changelog,README}.md \
    -t "$pkgdir/usr/share/doc/$pkgname/"
  install \
    -vDm 644 \
    license-mit \
    -t "$pkgdir/usr/share/licenses/$pkgname/"

  (
    cd \
      "$pkgdir"
    _pick \
      python-$pkgbase \
      usr/lib
  )
}

package_python-maturin() {
  pkgdesc+=" - Python bindings"
  depends=(
    maturin=$pkgver
    python
  )
  mv \
    -v \
    $pkgname/* \
    "$pkgdir"

  install \
    -vDm 644 \
    $pkgbase-$pkgver/license-mit \
    -t \
    "$pkgdir/usr/share/licenses/$pkgname/"
}

# vim:set sw=2 sts=-1 et:
