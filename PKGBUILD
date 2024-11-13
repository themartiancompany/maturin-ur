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
_pyver="$( \
  "${_py}" \
    -V | \
    awk \
      '{print $2}')"
_pymajver="${_pyver%.*}"
_pyminver="${_pymajver#*.}"
_pynextver="${_pymajver%.*}.$(( \
  ${_pyminver} + 1))"
_pkg=maturin
pkgbase="${_pkg}"
pkgname=(
  "${pkgbase}"
  "${_py}-${_pkg}"
)
pkgver=1.7.4
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
'72fb43a10d3cdf35eca224232c59723bff5b9230c7d40ff01537e1ba869254b4b6e9a0c52e8b7154f7b74f0f0b5bace4e7bb9ac5257c9fbc04750160791c35e3'
)
b2sums=(
  '008b2d67d553479040fcd3062b93ca12dc6f2b98c3d6cb43cac7ff0d8e4772417b99fbf2a73ed96cfcc8a7372b37d845e8485ba0afc80342943a10d716492377'
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
      'ndk-sysroot'
    )
  elif [[ "${_os}" == 'GNU/Linux' ]]; then
    depends+=(
      'glibc'
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
    "${_py}>=${_pymajver}"
    "${_py}<${_pynextver}"
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
