# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: Daniel M. Capella <polyzen@archlinux.org>
# Contributor: Jonas Witschel <diabonas@archlinux.org>
# Contributor: Philipp A. <flying-sheep@web.de>

# Check if new updates break python-engineio

_py="python"
_pkg=aiohttp
pkgname="${_py}-${_pkg}"
pkgver=3.10.0
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
 "${_py}"
 "${_py}-aiohappyeyeballs"
 "${_py}-aiosignal"
 "${_py}-attrs"
 "${_py}-frozenlist"
 "${_py}-multidict"
 "${_py}-yarl"
)
makedepends=(
  cython
  git
  npm
  "${_py}-build"
  "${_py}-installer"
  "${_py}-setuptools"
  "${_py}-wheel"
)
checkdepends=(
  gunicorn
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
source=(
  "$pkgname::git+${_url}#tag=v${pkgver}"
  "git+https://github.com/nodejs/llhttp.git"
)
b2sums=(
  'd8953e706641b861b85bd51be22b78146ff6e2ebe924f0c2d99f7c5b927524fcfde41690e49aa19a59e80e7f29a4f1f976d02f0e12e11e794936896e46046dc1'
  'SKIP'
)

prepare() {
  cd \
    "${pkgname}"
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
  sed \
    's|.install-cython ||' \
    -i \
    Makefile
  # This test calls the Python interpreter,
  # we need to make sure that the path
  # for the C extensions is correct there as well
  sed \
    -i "s/import {import_path!s}/import sys; sys.path.insert(0, '{os.environ['PYTHONPATH']}'); &/" \
    tests/test_circular_imports.py
  # Remove coverage testing
  sed \
    -i \
    '/--cov=/d' \
    setup.cfg
}

build() {
  cd \
    "${pkgname}"
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
    "${pkgname}"
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
    "${pkgname}"
  "${_py}" \
    -m \
      installer \
    --destdir="${pkgdir}" \
    dist/*.whl
}

# vim: ts=2 sw=2 et:
