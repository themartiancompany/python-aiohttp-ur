# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: Daniel M. Capella <polyzen@archlinux.org>
# Contributor: Jonas Witschel <diabonas@archlinux.org>
# Contributor: Philipp A. <flying-sheep@web.de>

# Check if new updates break python-engineio

_git="true"
_os="$( \
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _git="false"
fi
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
_pkg=aiohttp
pkgname="${_py}-${_pkg}"
pkgver=3.11.1
pkgrel=1
pkgdesc='HTTP client/server for asyncio'
arch=(
  x86_64
  aarch64
  i686
  armv7l
  mips
  pentium4
  powerpc
  arm
)
url="https://${_pkg}.readthedocs.io"
license=(
  "Apache-2.0"
)
depends=(
 "${_py}>=${_pymajver}"
 "${_py}<${_pynextver}"
 "${_py}-aiohappyeyeballs"
 "${_py}-aiosignal"
 "${_py}-attrs"
 "${_py}-frozenlist"
 "${_py}-multidict"
 "${_py}-yarl"
)
makedepends=(
  'cython'
  'npm'
  "${_py}-build"
  "${_py}-installer"
  "${_py}-setuptools"
  "${_py}-wheel"
)
checkdepends=(
  'gunicorn'
  "${_py}-aiodns"
  "${_py}-brotli"
  "${_py}-freezegun"
  "${_py}-proxy.py"
  "${_py}-pytest"
  "${_py}-pytest-mock"
  "${_py}-pytest-xdist"
  "${_py}-re-assert"
  "${_py}-time-machine"
  "${_py}-trustme"
  "${_py}-uvloop"
)
optdepends=(
  'gunicorn: to deploy using Gunicorn'
  "${_py}-aiodns: for fast DNS resolving"
  "${_py}-brotli: for Brotli transfer-encodings support"
)
_http="https://github.com"
_ns="aio-libs"
_url="${_http}/${_ns}/${_pkg}"
_tag_name="tag"
_tag="${pkgver}"
_tarname="${_pkg}-${pkgver}"
_pypi="https://pypi.io/packages/source"
source=()
b2sums=()
if [[ "${_git}" == "true" ]]; then
  makedepends+=(
    'git'
  )
  _src="${_tarname}::git+${_url}.git#${_tag_name}=${_tag}"
  _sum="SKIP"
  source+=(
    "git+https://github.com/nodejs/llhttp.git"
  )
  b2sums+=(
    'SKIP'
  )
elif [[ "${_git}" == "false" ]]; then
  _src="${_tarname}.tar.gz::${_pypi}/${_pkg::1}/${_pkg}/${_pkg}-${pkgver}.tar.gz"
  _sum="f6c398ccd4c4dec25f83c9ed63ef2f4e8e91d7cce650664a7195d03e010ab5690b09323b88e2654f812d09a777003211601a979126e355d7daa1603a3ff472b9"
fi
source+=(
  "${_src}"
)
b2sums+=(
  "${_sum}"
)

prepare() {
  cd \
    "${_tarname}"
  if [[ "${_git}" == "true" ]]; then
    git \
      submodule \
        init
    git \
      config \
        submodule.vendor/llhttp.url \
        ../llhttp
    git \
      -c \
        protocol.file.allow=always \
      submodule \
        update \
        --recursive
  fi
  sed \
    's|.install-cython ||' \
    -i \
    Makefile
  # This test calls the Python interpreter,
  # we need to make sure that the path
  # for the C extensions is correct there as well
  sed \
    -i \
    "s/import {import_path!s}/import sys; sys.path.insert(0, '{os.environ['PYTHONPATH']}'); &/" \
    tests/test_circular_imports.py
  # Remove coverage testing
  sed \
    -i \
    '/--cov=/d' \
    setup.cfg
}

build() {
  cd \
    "${_tarname}"
  make \
    generate-llhttp \
    cythonize
  "${_py}" \
    -m \
      build \
    --wheel \
    --no-isolation
}

check() {
  cd \
    "${_tarname}"
  local \
    python_version=$( \
      python \
        -c \
        'import sys; print("".join(map(str, sys.version_info[:2])))')
  # Docker tests
  mv \
    tests/autobahn/test_autobahn.py{,.bak}
  # https://github.com/aio-libs/aiohttp/issues/8234
  PYTHONPATH="$PWD/build/lib.linux-$CARCH-cpython-$python_version" \
  pytest \
    --deselect \
      'tests/test_pytest_plugin.py::test_aiohttp_plugin'
  mv \
    tests/autobahn/test_autobahn.py{.bak,}
}

package() {
  cd \
    "${_tarname}"
  "${_py}" \
    -m \
      installer \
    --destdir="${pkgdir}" \
    dist/*.whl
}

# vim: ts=2 sw=2 et:
b2sums=()
