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
_github="true"
_pypa="false"
_os="$( \
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _git="false"
  _pypa="false"
  _github="true"
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
_llhttp_pkgver=9.2.1
pkgrel=1
pkgdesc='HTTP client/server for asyncio'
arch=(
  'x86_64'
  'aarch64'
  'i686'
  'armv7l'
  'armv6l'
  'mips'
  'pentium4'
  'powerpc'
  'arm'
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
  "${_py}-propcache: cached properties"
)
_http="https://github.com"
_ns="aio-libs"
_url="${_http}/${_ns}/${_pkg}"
_llhttp_ns="nodejs"
_llhttp_url="${_http}/${_llhttp_ns}/llhttp"
_tag_name="tag"
_tag="${pkgver}"
_llhttp_tag="${_llhttp_pkgver}"
_tarname="${_pkg}-${pkgver}"
_llhttp_tarname="llhttp-${_llhttp_pkgver}"
if [[ "${_git}" == "true" ]]; then
  makedepends+=(
    'git'
  )
  _src="${_tarname}::git+${_url}.git#${_tag_name}=${_tag}"
  _sum="SKIP"
  _llhttp_src="${_llhttp_tarname}::git+${_llhttp_url}.git#${_tag_name}=${_llhttp_tag}"
  _llhttp_sum="SKIP"
elif [[ "${_git}" == "false" ]]; then
  if [[ "${_pypa}" == "true" ]]; then
    _pypi="https://pypi.io/packages/source"
    _src="${_tarname}.tar.gz::${_pypi}/${_pkg::1}/${_pkg}/${_pkg}-${pkgver}.tar.gz"
    _sum="whatever"
  elif [[ "${_github}" == "true" ]]; then
    _src="${_tarname}.tar.gz::${_url}/archive/refs/tags/v${pkgver}.tar.gz"
    _sum="35472ae5ec22bf653c743c2f467829d6fcd86565f0672646f90f17a071b5c9eca6a0349c85154fc8d2830d1a326d836395a5ea318980e53e4de6702183f7d2b2"
  fi
  _llhttp_src="${_llhttp_tarname}.tar.gz::${_llhttp_url}/archive/refs/tags/v${_llhttp_pkgver}.tar.gz"
  _llhttp_sum='5016e6cc7b4cd313ffcfb02ea2b8c8530510020b5727346236b4f8477ac1daca73883d99230fe312b688d5d8cb5252d5ef7e11bb4f914186e069f001c95ac401'
fi
source=(
  "${_src}"
  "${_llhttp_src}"
)
b2sums=(
  "${_sum}"
  "${_llhttp_sum}"
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
  elif [[ "${_git}" == "false" ]]; then
    cp \
      -r \
      "../${_llhttp_tarname}/"* \
      "vendor/llhttp"
  fi
  sed \
    's|.install-cython ||' \
    -i \
    Makefile
  # ${_url}/issues/9981
  sed \
    "s|aiohttp/hdrs.py|$(pwd)/aiohttp/hdrs.py|" \
    -i \
    'Makefile'
  sed \
    "s|aiohttp/hdrs.py|$(pwd)/aiohttp/hdrs.py|" \
    -i \
    'tools/gen.py'
  sed \
    "s|folder = ROOT / \"aiohttp\"|folder = ROOT / \"src/${_pkg}-${pkgver}/aiohttp\"|" \
    -i \
    'tools/gen.py'
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
