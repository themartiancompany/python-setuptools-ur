# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Contributor: Eli Schwartz <eschwartz@archlinux.org>

_git=true
_offline=false
_os="$( \
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _git=false
fi
_py="python"
_pyver="$( \
  "${_py}" \
    -V | \
    awk \
      '{print $2}')"
_pymajver="${_pyver%.*}"
_pkg="setuptools"
pkgname="${_py}-${_pkg}"
pkgver=75.1.0
_commit=b07d2f58233f9a99a820901924e263645c57a7c6
pkgrel=1
epoch=1
_pkgdesc=(
  "Easily download, build, install, upgrade,"
  "and uninstall Python packages"
)
pkgdesc="${_pkgdesc[*]}"
arch=(
  'any'
)
license=(
  'PSF'
)
url="https://pypi.org/project/${_pkg}"
depends=(
  "${_py}>=${_pymajver}"
  "${_py}-jaraco.text"
  "${_py}-more-itertools"
  "${_py}-ordered-set"
  "${_py}-packaging"
  "${_py}-platformdirs"
  "${_py}-tomli"
  "${_py}-validate-pyproject"
)
makedepends=(
  "${_py}-${_pkg}"
)
[[ "${_git}" == true ]] && \
  makedepends+=(
    'git'
  )
checkdepends=(
  "${_py}-jaraco.envs"
  "${_py}-jaraco.path"
  "${_py}-pip"
  "${_py}-pytest-fixture-config"
  "${_py}-pytest-virtualenv"
  "${_py}-wheel"
  "${_py}-pytest-enabler"
  "${_py}-pytest-mypy"
  "${_py}-pytest-timeout"
  "${_py}-sphinx"
  "${_py}-build"
  "${_py}-ini2toml"
  "${_py}-tomli-w"
)
provides=(
  "${_py}-distribute=${pkgver}"
)
replaces=(
  "${_py}-distribute"
)
_url="https://github.com/pypa/${_pkg}"
_tag_name="commit"
_tag="${_commit}"
if [[ "${_git}" == "true" ]]; then
  _source="${_pkg}-${pkgver}::git+${_url}#${_tag_name}=${_tag}?signed"
  if [[ "${_offline}" == "true" ]]; then
    _source="${_pkg}-${pkgver}::git+file://${HOME}/${_pkg}"
  fi
  _sum='SKIP'
else
  _source="${_pkg}-${pkgver}.tar.gz::${_url}/archive/refs/tags/v${pkgver}.tar.gz"
  _sum="9ce0ea0c1e85db54ff54235198edba36ec1dd5251dd9ecccdadc5793a5d92264f04e97d521b416e9243e910fd9ef6e87991c5bbf6845d072b9051bb865d8d380"
fi
source=(
  "${_source}"
  system-validate-pyproject.patch
  add-dependency.patch
  build-no-isolation.patch
)
sha512sums=(
  "${_sum}"
  '390fea2c575a0042054f51d33e629b04a48f832f0a4a2dd07d34e23cdf330c382dba0f54bfb7c8a6a253bb248a4940f2a789672f715e4dc2aeb395fa185cae7a'
  '9c5d80c753e78bf613572fb789a234984087d0ce96d0bad22b5ed731d83c77bf6d8acfa65c78f6c78f9063be7819c2b58988fdf8e7fc89b55339f94a87b3b21f'
  '5b03349d09a6f1caff65684e746d3598f14172c3f4bd54981600598895baf08ba5cfaa3b0616af8ba0c1de607ca14cca8233358e5e34a7b9478f2522c0153ad5'
)
export \
  SETUPTOOLS_INSTALL_WINDOWS_SPECIFIC_FILES=0

prepare() {
  ls
  cd \
    "${_pkg}-${pkgver}"
  # patch \
  #   -p1 \
  #   -i \
  #   ../system-validate-pyproject.patch
  rm \
    -rf \
    {pkg_resources,"${_pkg}"}/{extern,_vendor} \
    "${_pkg}/config/_validate_pyproject" || \
    true
  # Upstream devendoring logic is badly broken, see:
  # https://bugs.archlinux.org/task/58670
  # https://github.com/pypa/pip/issues/5429
  # https://github.com/pypa/setuptools/issues/1383
  # The simplest fix is to simply rewrite
  # import paths to use the canonical
  # location in the first place
  for _module \
    in "${_pkg}" \
       pkg_resources \
       '' ; do
    find \
      . \
      -name \
        \*.py \
      -exec \
        sed \
          -i \
          -e \
            's/from '$_module.extern' import/import/' \
          -e \
            's/from '$_module.extern'\./from /' \
          -e \
            's/import '$_module.extern'\./import /' \
          -e \
            "s/__import__('$_module.extern./__import__('/" \
          -e \
            's/from \.\.extern\./from /' \
          {} +
  done
  # Add the devendored dependencies into metadata of setuptools
  # patch \
  #   -p1 \
  #   -i \
  #   ../add-dependency.patch
  # Fix tests invoking python-build
  # patch \
  #   -p1 \
  #   -i \
  #   ../build-no-isolation.patch
  # Remove post-release tag since we are using stable tags
  sed \
    -e \
      '/tag_build = .post/d' \
    -e \
      '/tag_date = 1/d' \
    -i \
    setup.cfg
  # Fix shebang
  sed \
    -i \
    -e \
    "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python3|" \
    "${_pkg}/command/easy_install.py"
}

build() {
  cd \
    "${_pkg}-${pkgver}"
  "${_py}" \
    setup.py \
      build
}

check() { (
  # Workaround UTF-8
  # tests by setting LC_CTYPE
  export \
    LC_CTYPE=en_US.UTF-8
  # https://github.com/pypa/setuptools/pull/810
  export \
    PYTHONDONTWRITEBYTECODE=1
  cd \
    "${_pkg}-${pkgver}"
  # 1,4: subtle difference introduced by devendoring
  # 2: pip failures related to devendoring, 
  # 3,5: TODO
  # 6: jaraco.develop is not packaged
  PYTHONPATH="$PWD"/build/lib \
    python \
      -m \
        pytest \
      --deselect \
        "${_pkg}/tests/config/test_apply_pyprojecttoml.py::test_apply_pyproject_equivalent_to_setupcfg" \
      --deselect \
        "${_pkg}/tests/test_virtualenv.py" \
      --deselect \
        "${_pkg}/tests/test_editable_install.py::test_editable_with_prefix" \
      --deselect \
        "${_pkg}/_normalization.py::setuptools._normalization.safe_version" \
      --deselect \
        "${_pkg}/tests/test_easy_install.py::TestSetupRequires::test_setup_requires_honors_fetch_params" \
      --ignore \
        tools/finalize.py
)}

package() {
  cd \
    "${_pkg}-${pkgver}"
  "${_py}" \
    setup.py \
      install \
        --prefix=/usr \
	--root="${pkgdir}" \
	--optimize=1 \
	--skip-build
}

# vim:set sw=2 sts=-1 et:
