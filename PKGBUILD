# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgbase=python-setuptools
pkgname=('python-setuptools' 'python2-setuptools')
pkgver=34.4.1
pkgrel=1
epoch=1
pkgdesc="Easily download, build, install, upgrade, and uninstall Python packages"
arch=('any')
license=('PSF')
url="http://pypi.python.org/pypi/setuptools"
makedepends=('python-packaging' 'python2-packaging' 'python-appdirs' 'python2-appdirs' 'python-pip'
             'python2-pip')
checkdepends=('python-pytest-runner' 'python2-pytest-runner' 'python-pytest-flake8'
              'python2-pytest-flake8' 'python2-backports.unittest_mock' 'git')
source=("$pkgbase-$pkgver.tar.gz::https://github.com/pypa/setuptools/archive/v$pkgver.tar.gz")
sha512sums=('b0a7475ccb072e78706944d6d794f7de32d1a4d507765e9d6b5ec95a4776472d8e00cc0e327b0f83a985c20caf92d0d24a1745d5464dd6fff3983abdc30ce445')

prepare() {
  # Don't download and install deps
  sed -i '/pip.main(args)/d' setuptools-$pkgver/bootstrap.py

  # Remove post-release tag since we are using stable tags
  sed -e '/tag_build = .post/d' \
      -e '/tag_date = 1/d' \
      -i setuptools-$pkgver/setup.cfg

  cp -a setuptools-$pkgver{,-py2}

  cd "$srcdir"/setuptools-$pkgver
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python3|" setuptools/command/easy_install.py

  cd "$srcdir"/setuptools-$pkgver-py2
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python2|" setuptools/command/easy_install.py
  sed -i -e "s|'pip'|'pip2'|" setuptools/tests/{test_develop.py,test_namespaces.py}

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
