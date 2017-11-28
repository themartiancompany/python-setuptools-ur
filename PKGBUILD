# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgbase=python-setuptools
pkgname=('python-setuptools' 'python2-setuptools')
pkgver=38.2.3
pkgrel=1
epoch=1
pkgdesc="Easily download, build, install, upgrade, and uninstall Python packages"
arch=('any')
license=('PSF')
url="http://pypi.python.org/pypi/setuptools"
makedepends=('python-packaging' 'python2-packaging' 'python-appdirs' 'python2-appdirs' 'python-pip'
             'python2-pip')
checkdepends=('python-pytest-runner' 'python2-pytest-runner' 'python-pytest-flake8'
              'python2-pytest-flake8' 'python-mock' 'python2-mock' 'python-pytest-fixture-config'
              'python2-pytest-fixture-config' 'python-pytest-virtualenv' 'python2-pytest-virtualenv'
              'python-wheel' 'python2-wheel' 'git')
source=("$pkgbase-$pkgver.tar.gz::https://github.com/pypa/setuptools/archive/v$pkgver.tar.gz")
sha512sums=('cab6bfe44e6b2b83bf445979c5eeaff8e26c795489646c3e72f459d046ba28d89a7b8f71993463526c9f9af4261a25778ad488b0bdeb861f9693c4698644bfcd')

prepare() {
  # Don't download and install deps
  sed -i '/pip.main(args)/d' setuptools-$pkgver/bootstrap.py

  rm -r setuptools-$pkgver/pkg_resources/_vendor

  # Remove post-release tag since we are using stable tags
  sed -e '/tag_build = .post/d' \
      -e '/tag_date = 1/d' \
      -i setuptools-$pkgver/setup.cfg

  # 'Clean' installation is expected to fail since we removed bundled packages
  sed -i '/^def test_clean_env_install/i import pytest\n\n@pytest.mark.xfail' setuptools-$pkgver/setuptools/tests/test_virtualenv.py

  # Tests failed. Importing an unbundled new setuptools in a virtualenv does not work, but this won't
  # affect normal virtualenv usage (which don't have to import the unbundled setuptools in *current*
  # dir.
  sed -e '/^def test_pip_upgrade_from_source/i @pytest.mark.xfail' \
      -e '/^def test_test_command_install_requirements/i @pytest.mark.xfail' \
      -i setuptools-$pkgver/setuptools/tests/test_virtualenv.py

  cp -a setuptools-$pkgver{,-py2}

  cd "$srcdir"/setuptools-$pkgver
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python3|" setuptools/command/easy_install.py

  cd "$srcdir"/setuptools-$pkgver-py2
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python2|" setuptools/command/easy_install.py

  export SETUPTOOLS_INSTALL_WINDOWS_SPECIFIC_FILES=0
}

build() {
  cd "$srcdir"/setuptools-$pkgver
  python bootstrap.py
  python setup.py build

  cd "$srcdir"/setuptools-$pkgver-py2
  python2 bootstrap.py
  python2 setup.py build
}

check() { (
  # Workaround UTF-8 tests by setting LC_CTYPE
  export LC_CTYPE=en_US.UTF-8

  # https://github.com/pypa/setuptools/pull/810
  export PYTHONDONTWRITEBYTECODE=1

  cd "$srcdir"/setuptools-$pkgver
  python setup.py pytest

  cd "$srcdir"/setuptools-$pkgver-py2
  python2 setup.py pytest
)}

package_python-setuptools() {
  depends=('python-packaging' 'python-appdirs')
  provides=('python-distribute')
  replaces=('python-distribute')

  cd "$srcdir"/setuptools-$pkgver
  python setup.py install --prefix=/usr --root="$pkgdir" --optimize=1 --skip-build
}

package_python2-setuptools() {
  depends=('python2-packaging' 'python2-appdirs')
  provides=('python2-distribute')
  replaces=('python2-distribute')

  cd "$srcdir"/setuptools-$pkgver-py2
  python2 setup.py install --prefix=/usr --root="$pkgdir" --optimize=1 --skip-build
  rm "$pkgdir"/usr/bin/easy_install
}
