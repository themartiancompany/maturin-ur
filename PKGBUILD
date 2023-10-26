# Maintainer: David Runge <dvzrv@archlinux.org>

pkgbase=maturin
pkgname=(
  maturin
  python-maturin
)
pkgver=1.3.1
pkgrel=1
pkgdesc="Build and publish crates with pyo3, rust-cpython and cffi bindings"
url="https://github.com/PyO3/maturin"
arch=(x86_64)
license=('Apache-2.0 OR MIT')
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
sha512sums=('71d222f9038359028b13c09a69f27b1c5e8cad8b75e79f09ef4dd97061d31d8d26e017aad90263b72b13a84da3819419df7c527dbd7e4bd14e8ac17e0ac985bd')
b2sums=('d24262dfb66d548f63c700af774fd176ae995a5dade34f86fd616d6839ea27e27c20c85259c6fd350f7e3559e11b735672f75e3af178cd2dcdb6370bd565c02f')

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
  cd $pkgbase-$pkgver
  cargo fetch --locked --target "$CARCH-unknown-linux-gnu"
}

build() {
  cd $pkgbase-$pkgver
  python -m build --wheel --no-isolation
}

package_maturin() {
  depends=(
    bzip2
    gcc-libs
    glibc
    rust
  )

  cd $pkgbase-$pkgver
  python -m installer --destdir="$pkgdir" dist/*.whl
  install -vDm 644 {Changelog,README}.md -t "$pkgdir/usr/share/doc/$pkgname/"
  install -vDm 644 license-mit -t "$pkgdir/usr/share/licenses/$pkgname/"

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

  install -vDm 644 $pkgbase-$pkgver/license-mit -t "$pkgdir/usr/share/licenses/$pkgname/"
}
