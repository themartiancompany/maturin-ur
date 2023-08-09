# Maintainer: David Runge <dvzrv@archlinux.org>

pkgbase=maturin
pkgname=(
  maturin
  python-maturin
)
pkgver=1.2.0
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
sha512sums=('5c7f5f66c9c5b53244c31bc40cf7af6fc326d56d0902029595258ee802c2e7f4262550613770bd00cb0c5812767a9044de255c121a8be0467259626b8e4c27b8')
b2sums=('f55fa0837ba489292acd5481ac6acbc7ac6b073cb3bf910b920f3045a3caf694dea015aceb4e1b9029807a12b26165d7ea649e1e9c0c3a4b5dfe3fdd0cd24858')

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
