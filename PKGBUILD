# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2024, 2025, 2026
#                Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainers:
#   Truocolo
#     <truocolo@aol.com>
#     <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
#   Pellegrino Prevete (dvorak)
#     <pellegrinoprevete@gmail.com>
#     <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
#   Levente Polyak
#     <anthraxx[at]archlinux[dot]org>
#   Daniel M. Capella
#     <polyzen@archlinux.org>
# Contributors:
#   Jonas Witschel
#     <diabonas@archlinux.org>
#   Philipp A.
#     <flying-sheep@web.de>

# Check if new updates break python-engineio

_os="$(
  uname \
    -o)"
_evmfs_available="$(
  command \
    -v \
    "evmfs" || \
    true)"
if [[ ! -v "_evmfs" ]]; then
  if [[ "${_evmfs_available}" != "" ]]; then
    _evmfs="true"
  elif [[ "${_evmfs_available}" == "" ]]; then
    _evmfs="false"
  fi
fi
if [[ ! -v "_git" ]]; then
  if [[ "${_evmfs}" == "true" ]]; then
    _git="false"
  elif [[ "${_evmfs}" == "false" ]]; then
    _git="false"
  fi
fi
if [[ ! -v "_offline" ]]; then
  _offline="false"
fi
if [[ ! -v "_git_service" ]]; then
  if [[ "${_evmfs}" == "true" ]]; then
    _git_service="gitlab"
  elif [[ "${_evmfs}" == "false" ]]; then
    _git_service="github"
  fi
fi
if [[ ! -v "_git_http" ]]; then
  _git_http="${_git_service}"
fi
if [[ ! -v "_ns" ]]; then
  if [[ "${_git_service}" == "github"  ]]; then
    _ns="aio-libs"
  elif [[ "${_git_service}" == "gitlab"  ]]; then
    _ns="themartiancompany"
  fi
fi
if [[ ! -v "_tag_name" ]]; then
  if [[ "${_evmfs}" == "true" ]]; then
    _tag_name="commit"
    _llhttp_tag_name="commit"
  elif [[ "${_evmfs}" == "false" ]]; then
    if [[ "${_git}" == "true" ]]; then
      _tag_name="commit"
      _llhttp_tag_name="commit"
    elif [[ "${_git}" == "false" ]]; then
      _tag_name="tag"
      _llhttp_tag_name="tag"
    fi
  fi
fi
if [[ ! -v "_archive_format" ]]; then
  if [[ "${_git}" == "true" ]]; then
    if [[ "${_evmfs}" == "true" ]]; then
      _archive_format="bundle"
    elif [[ "${_evmfs}" == "false" ]]; then
      _archive_format="git"
    fi
  elif [[ "${_git}" == "false" ]]; then
    if [[ "${_git_service}" == "github" ]]; then
      if [[ "${_tag_name}" == "commit" ]]; then
        _archive_format="zip"
      elif [[ "${_tag_name}" == "tag" ]]; then
        _archive_format="tar.gz"
      fi
    elif [[ "${_git_service}" == "gitlab" ]]; then
      _archive_format="tar.gz"
    fi
  fi
fi
if [[ ! -v "_pypa" ]]; then
  if [[ "${_evmfs}" == "true" ]]; then
    _pypa="false"
  elif [[ "${_evmfs}" == "false" ]]; then
    _pypa="true"
  fi
fi
_py="python"
_pyver="$(
  "${_py}" \
    -V | \
    awk \
      '{print $2}')"
_pymajver="${_pyver%.*}"
_pyminver="${_pymajver#*.}"
_pynextver="${_pymajver%.*}.$((
  ${_pyminver} + 1))"
_pkg=aiohttp
pkgbase="${_py}-${_pkg}"
pkgname=(
  "${pkgbase}"
)
pkgver=3.11.1
_commit="fe1196c20c86d201990be45f4f0f4b2b167913ad"
_llhttp_pkgver=9.2.1
_llhttp_commit="b0b279fb5a617ab3bc2fc11c5f8bd937aac687c1"
pkgrel=24
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
  "${_py}"
  "${_py}-build"
  "${_py}-installer"
  "${_py}-setuptools"
  "${_py}-wheel"
  "${_py}-multidict"
)
if [[ "${_pypa}" == "true" ]]; then
  makedepends+=(
    "${_py}-pip"
)
fi
if [[ "${_git}" == "true" ]]; then
  makedepends+=(
    'git'
  )
  if [[ "${_evmfs}" == "false" ]]; then
    makedepends+=(
      "typescript"
    )
  fi
fi
if [[ "${_evmfs}" == "true" ]]; then
  makedepends+=(
    "evmfs"
  )
fi
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
_http="https://${_git_service}.com"
source=()
sha256sums=()
_url="${_http}/${_ns}/${_pkg}"
if [[ ! -v "_tag" ]]; then
  if [[ "${_tag_name}" == "tag" ]]; then
    _tag="${pkgver}"
    _llhttp_tag="${_llhttp_pkgver}"
  elif [[ "${_tag_name}" == "commit" ]]; then
    _tag="${_commit}"
    _llhttp_tag="${_llhttp_commit}"
  fi
fi
_tarname="${_pkg}-${_tag}"
_tarfile="${_tarname}.${_archive_format}"
if [[ "${_offline}" == "true" ]]; then
  _url="file://${HOME}/${pkgname}"
fi
_llhttp_ns="nodejs"
_llhttp_url="${_http}/${_llhttp_ns}/llhttp"
_llhttp_tarname="llhttp-${_llhttp_tag}"
_github_release_sum="63bad361ad74e56a36a11f9fde50370c105153380b24c7e13c0e2dfe3698d352"
_github_release_sig_sum="4891e24b7b94b10ab60830f39f89a5b6afda62d5238c31d45c6915ea6a3ade83"
_gitlab_sum="e975ef3dd65c17396dbfb605ef43614dcb0abd59b45fb32498ffa07321688bf4"
_gitlab_sig_sum="baaab158cd64ab456ed368f43d82c2a9dc02e9074e5fcbabe86f0a2cd9954298"
_github_sum="86944e981cdaad57ab456b5ed39967649e9d0d3d3355ea4fc44234d4cd4aa934"
_github_sig_sum="f822f8faade3a590a3abc20d480837ce9f9fcbdddeac3e5e3326f280ebacad4b"
_llhttp_sum="9b8a4838b5813c4ad11dfb79e695bd867b493ec7f13aae00a7567c64c10613a5"
_llhttp_sig_sum="f5d3d61c7088e540ec5fd03c70bfe835f1120b524d00ec45a0a6c4d9e438fb8b"
_pypa_sum="1e59dc724138b264de2e7862b3953ab5db518df280b4fd60af6a1f54b229ebb7"
_pypa_sig_sum="64815d19637c0495371ce4ee969a360a0dac914db0eb7d2a11ad6dbffae0713b"
_bundle_sum="SKIP"
_bundle_sig_sum="SKIP"
_llhttp_bundle_sum="SKIP"
_llhttp_bundle_sig_sum="SKIP"
if [[ "${_evmfs}" == "true" ]]; then
  if [[ "${_git}" == "true" ]]; then
    _sum="${_bundle_sum}"
    _sig_sum="${_bundle_sig_sum}"
    # Truocolo
    _evmfs_ns="0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b"
  elif [[ "${_git}" == "false" ]]; then
    # Truocolo
    _evmfs_ns="0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b"
    _sum="${_github_sum}"
    _sig_sum="${_github_sig_sum}"
    if [[ "${_git_service}" == "github" ]]; then
      _sum="${_github_sum}"
      _sig_sum="${_github_sig_sum}"
      # Truocolo
      _evmfs_ns="0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b"
    fi
  fi
elif [[ "${_evmfs}" == "false" ]]; then
  if [[ "${_git}" == "true" ]]; then
    _sum="SKIP"
    _sig_sum="SKIP"
    # Truocolo
    _evmfs_ns="0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b"
  elif [[ "${_git}" == "false" ]]; then
    if [[ "${_pypa}" == "true" ]]; then
      _sum="${_pypa_sum}"
      _sig_sum="${_pypa_sig_sum}"
      # Truocolo
      _evmfs_ns="0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b"
    elif [[ "${_git_service}" == "github" ]]; then
      _sum="${_github_sum}"
      _sig_sum="${_github_sig_sum}"
      # Truocolo
      _evmfs_ns="0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b"
    elif [[ "${_git_service}" == "gitlab" ]]; then
      _sum="${_gitlab_sum}"
      _sig_sum="${_gitlab_sig_sum}"
      # Truocolo
      _evmfs_ns="0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b"
    fi
  fi
fi
_evmfs_network="100"
_evmfs_address="0x69470b18f8b8b5f92b48f6199dcb147b4be96571"
_evmfs_dir="evmfs://${_evmfs_network}/${_evmfs_address}/${_evmfs_ns}"
_evmfs_uri="${_evmfs_dir}/${_sum}"
_evmfs_src="${_tarfile}::${_evmfs_uri}"
_llhttp_evmfs_uri="${_evmfs_dir}/${_llhttp_sum}"
_llhttp_evmfs_src="${_llhttp_tarname}.tar.gz::${_llhttp_evmfs_uri}"
_evmfs_src="${_tarfile}::${_evmfs_uri}"
_sig_uri="${_evmfs_dir}/${_sig_sum}"
_sig_src="${_tarfile}.sig::${_sig_uri}"
if [[ "${_evmfs}" == "true" ]]; then
  if [[ "${_git}" == "true" ]]; then
    _llhttp_src="${_llhttp_tarname}::git+${_llhttp_url}.git#${_tag_name}=${_llhttp_tag}"
    _llhttp_sum="SKIP"
  elif [[ "${_git}" == "false" ]]; then
    _llhttp_uri="${_llhttp_url}/archive/refs/tags/v${_llhttp_pkgver}.tar.gz"
    _llhttp_src="${_llhttp_tarname}.tar.gz::${_llhttp_uri}"
    _llhttp_sum='SKIP'
  fi
  _src="${_evmfs_src}"
  source+=(
    "${_sig_src}"
  )
  sha256sums+=(
    "${_sig_sum}"
  )
elif [[ "${_evmfs}" == "false" ]]; then
  if [[ "${_git}" == "true" ]]; then
    _src="${_tarname}::git+${_url}.git#${_tag_name}=${_tag}"
    _llhttp_src="${_llhttp_tarname}::git+${_llhttp_url}.git#${_tag_name}=${_llhttp_tag}"
    _llhttp_sum="SKIP"
  elif [[ "${_git}" == "false" ]]; then
    if [[ "${_pypa}" == "true" ]]; then
      _pypi="https://pypi.io/packages/source"
      _src="${_tarname}.tar.gz::${_pypi}/${_pkg::1}/${_pkg}/${_pkg}-${pkgver}.tar.gz"
    elif [[ "${_git_service}" == "github" ]]; then
      if [[ "${_tag_name}" == "tag" ]]; then
        _uri="${_url}/archive/refs/tags/v${pkgver}.tar.gz"
        _src="${_tarfile}::${_uri}"
        _sum="${_github_release_sum}"
      elif [[ "${_tag_name}" == "commit" ]]; then
        _uri="${_url}/archive/${_commit}.${_archive_format}"
        _src="${_tarfile}::${_uri}"
        _sum="${_github_sum}"
        _llhttp_sum='SKIP'
      fi
    elif [[ "${_git_service}" == "gitlab" ]]; then
      _src="hurr"
      _sum="whatever"
      _llhttp_sum='SKIP'
    fi
    _llhttp_src="${_llhttp_tarname}.tar.gz::${_llhttp_url}/archive/refs/tags/v${_llhttp_pkgver}.tar.gz"
  fi
fi
source+=(
  "${_src}"
  "${_llhttp_src}"
)
sha256sums+=(
  "${_sum}"
  "${_llhttp_sum}"
)
# b2sums=(
#   "${_sum}"
#   "${_llhttp_sum}"
# )

prepare() {
  ls \
    -lsh
  cd \
    "${_tarname}"
  ls \
    -lsh
  if [[ "${_git}" == "true" ]]; then
    git \
      submodule \
        init
    git \
      config \
        "submodule.vendor/llhttp.url" \
        "../llhttp-${_llhttp_tag}"
    git \
      -c \
        "protocol.file.allow=always" \
      submodule \
        update \
        --recursive
  elif [[ "${_git}" == "false" ]]; then
    if [[ "${_pypa}" == "false" ]]; then
      mkdir \
        ".git"
      mkdir \
        -p \
        "vendor/llhttp"
      ls \
        -lsh \
        "vendor"
      # if [[ "${_llhttp_tag_name}" == "tag" ]]; then
      cp \
        -r \
        "../${_llhttp_tarname}/"* \
        "vendor/llhttp"
      sed \
        's|.install-cython ||' \
        -i \
        Makefile
    fi
  fi
 if [[ -e "tools/gen.py" ]]; then
    sed \
      "s|a${_pkg}/hdrs.py|${PWD}/${_pkg}/hdrs.py|" \
      -i \
      'Makefile'
    sed \
      "s|${_pkg}/hdrs.py|${PWD}/${_pkg}/hdrs.py|" \
      -i \
      'tools/gen.py' || \
      true
    sed \
      "s|folder = ROOT / \"${_pkg}\"|folder = ROOT / \"src/${_pkg}-${pkgver}/${_pkg}\"|" \
      -i \
      'tools/gen.py'
  fi
  # This test calls the Python interpreter,
  # we need to make sure that the path
  # for the C extensions is correct there as well
  sed \
    -i \
    "s/import {import_path!s}/import sys; sys.path.insert(0, '{os.environ['PYTHONPATH']}'); &/" \
    "tests/test_circular_imports.py"
  # Remove coverage testing
  sed \
    '/--cov=/d' \
    -i \
    "setup.cfg"
  # Remove line 56 and 57 from Makefile
  # sed \
  #   "" \
  #   -i \
  #   "Makefile"
}

build() {
  cd \
    "${_tarname}"
  make \
    generate-llhttp
  if [[ "${_pypa}" == "false" ]]; then
    make \
      cythonize
  fi
  "${_py}" \
    -m \
      build \
    --wheel \
    --no-isolation
}

check() {
  local \
    _python_version
  cd \
    "${_tarname}"
  _python_version=$(
    python \
      -c \
      'import sys; print("".join(map(str, sys.version_info[:2])))')
  # Docker tests
  mv \
    "tests/autobahn/test_autobahn.py"{"",".bak"}
  # https://github.com/aio-libs/aiohttp/issues/8234
  PYTHONPATH="${PWD}/build/lib.linux-${CARCH}-cpython-${_python_version}" \
  pytest \
    --deselect \
      'tests/test_pytest_plugin.py::test_aiohttp_plugin'
  mv \
    "tests/autobahn/test_autobahn.py"{".bak",""}
}

package() {
  cd \
    "${_tarname}"
  "${_py}" \
    -m \
      "installer" \
    --destdir="${pkgdir}" \
    "dist/"*".whl"
}

# vim: ts=2 sw=2 et:
