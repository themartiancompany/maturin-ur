# Maintainer: David Runge <dvzrv@archlinux.org>

pkgbase=maturin
pkgname=(
  maturin
  python-maturin
)
pkgver=1.0.1
pkgrel=1
pkgdesc="Build and publish crates with pyo3, rust-cpython and cffi bindings"
url="https://github.com/PyO3/maturin"
arch=(x86_64)
license=(Apache)
makedepends=(
  bzip2
  gcc-libs
  git
  glibc
  python-build
  python-installer
  python-setuptools
  python-setuptools-rust
  python-wheel
  rust
)
# disable LTO until ring can be built with it: https://github.com/briansmith/ring/issues/1444
options=(!lto)
source=($url/archive/v$pkgver/$pkgname-v$pkgver.tar.gz)
sha512sums=('d43b7c97cdb28e650ce8677ae3dff5a4cabdeed3f8326b8e12f5f725d3c8983c5406dcbcff9f79f339d9206a7337eb7e16879dfade12f212549926cdaa92c439')
b2sums=('e1b25c06deefed1438c68cadbbdf6eeec541125e1833e95de87e55a3ea6c8f8aee81b217966fa856d082b0c2f62cbbe414073d5bd797a573012ad53c99be11b0')

_pick() {
  local p="$1" f d; shift
  for f; do
    d="$srcdir/$p/${f#$pkgdir/}"
    mkdir -p "$(dirname "$d")"
    mv "$f" "$d"
    rmdir -p --ignore-fail-on-non-empty "$(dirname "$f")"
  done
}

prepare() {
  cd $pkgname-$pkgver
  cargo fetch --locked --target "$CARCH-unknown-linux-gnu"
}

build() {
  cd $pkgname-$pkgver
  python -m build --wheel --no-isolation
}

package_maturin() {
  depends=(
    bzip2
    gcc-libs
    glibc
    rust
  )

  cd $pkgname-$pkgver
  python -m installer --destdir="$pkgdir" dist/*.whl
  install -vDm 644 {Changelog,README}.md -t "$pkgdir/usr/share/doc/$pkgname/"

  (
    cd "$pkgdir"
    _pick python-$pkgbase usr/lib
  )
}

package_python-maturin() {
  pkgdesc+=" - Python bindings"
  depends=(
    maturin=$pkgver
    python
  )

  mv -v $pkgname/* "$pkgdir"
}
