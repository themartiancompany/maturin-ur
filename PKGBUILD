# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer:  Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Maintainer:  Truocolo <truocolo@aol.com>
# Maintainer:  David Runge <dvzrv@archlinux.org>

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
pkgver=1.5.0
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
  armv7l
  aarch64
  i686
  pentium4
  mips
)
license=(
  'Apache-2.0 OR MIT'
)
makedepends=(
  bzip2
  git
  "${_py}-build"
  "${_py}-installer"
  "${_py}-setuptools"
  "${_py}-setuptools-rust"
  "${_py}-wheel"
  rust
)
if [[ "${_os}" == 'Android' ]]; then
  makedepends+=(
    ndk-sysroot
  )
elif [[ "${_os}" == 'GNU/Linux' ]]; then
  makedepends+=(
    glibc
    gcc-libs
  )
fi
# disable LTO until ring can be built with it:
# https://github.com/briansmith/ring/issues/1444
options=(
  !lto
)
source=(
  "${url}/archive/v${pkgver}/${pkgname}-v${pkgver}.tar.gz"
)
sha512sums=(
  'fa30ceafae02b72bae772ee0cb99af1394f258ef37574b9a46cec9528615cac896cdf9a0540c5c5a9b0d7500993d8313c1afc24d4b5337c43d0fbcf8203d8048'
)
b2sums=(
  '804198a313aa413c251e4dfd3f399bb9c5826234a9caf1422d770e4becca85b79d237c11c4920b60fee43550d174ffb9b7dfb12036af89fc31e6a36e7e3f317d'
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
    rust
  )
  if [[ "${_os}" == 'Android' ]]; then
    depends+=(
      ndk-sysroot
    )
  elif [[ "${_os}" == 'GNU/Linux' ]]; then
    depends+=(
      glibc
    )
  fi
  cd \
    "${pkgbase}-${pkgver}"
  "${_py}" \
    -m installer \
    --destdir="${pkgdir}" \
    dist/*.whl
  install \
    -vDm 644 \
    {Changelog,README}.md \
    -t "${pkgdir}/usr/share/doc/${pkgname}/"
  install \
    -vDm 644 \
    license-mit \
    -t "${pkgdir}/usr/share/licenses/${pkgname}/"

  (
    cd \
      "${pkgdir}"
    _pick \
      "${_py}-${_pkg}" \
      usr/lib
  )
}

package_python-maturin() {
  pkgdesc+=" - Python bindings"
  depends=(
    "${_pkg}=${pkgver}"
    "${_py}"
  )
  mv \
    -v \
    "${_py}-${_pkg}/"* \
    "${pkgdir}"

  install \
    -vDm 644 \
    "${pkgbase}-${pkgver}/license-mit" \
    -t \
    "${pkgdir}/usr/share/licenses/${pkgname}/"
}

# vim:set sw=2 sts=-1 et:
