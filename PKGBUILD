# Maintainer: David Runge <dvzrv@archlinux.org>

pkgbase=maturin
pkgname=(
  maturin
  python-maturin
)
pkgver=1.0.0
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
sha512sums=('69af5162134c6b0601b7bab3f6198085633e616960edcbf2792a77a6af756da9d9237e49c8a190cd68c7694f0c63fa4d80d661c1bf3786c261edbac0f80cafcf')
b2sums=('246abc31bd5f9d52a6ec76e631e14dce5574ee578a79ab400514c4bd6cd3c1df3709b9da3484680a88580310d28542fbefad3b0e6e6a998c648c077f8f3d5ad1')

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
