# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgbase=python-setuptools
pkgname=('python-setuptools' 'python2-setuptools')
pkgver=20.6.7
pkgrel=1
epoch=1
pkgdesc="Easily download, build, install, upgrade, and uninstall Python packages"
arch=('any')
license=('PSF')
url="http://pypi.python.org/pypi/setuptools"
makedepends=('python-packaging' 'python2-packaging')
checkdepends=('python-mock' 'python2-mock' 'python-pytest-runner' 'python2-pytest-runner' 'git')
source=("https://pypi.python.org/packages/source/s/setuptools/setuptools-$pkgver.tar.gz")
sha512sums=('91a212b5007f9fdfacb4341e06dc0355c5c29897eb8ea407dd4864091f845ba1417bb0d33b5ed6897869d0233e2d0ec6548898d3dbe9eda23f751829bd51a104')

prepare() {
  # We ship separated python{,2}-{packaging,six} packages.
  rm -rv setuptools-$pkgver/pkg_resources/_vendor

  cp -a setuptools-${pkgver}{,-py2}

  cd "$srcdir"/setuptools-$pkgver
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python3|" setuptools/command/easy_install.py

  cd "$srcdir"/setuptools-$pkgver-py2
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python2|" setuptools/command/easy_install.py
}

build() {
  cd "$srcdir"/setuptools-$pkgver
  python3 setup.py build

  cd "$srcdir"/setuptools-$pkgver-py2
  python2 setup.py build
}

check() {
  # Workaround UTF-8 tests by setting LC_CTYPE

  cd "$srcdir"/setuptools-$pkgver
  LC_CTYPE=en_US.utf8 python3 setup.py ptr

  cd "$srcdir"/setuptools-$pkgver-py2
  LC_CTYPE=en_US.utf8 python2 setup.py ptr
}
 
package_python-setuptools() {
  depends=('python-packaging')
  provides=('python-distribute')
  replaces=('python-distribute')

  cd "$srcdir"/setuptools-$pkgver
  python3 setup.py install --prefix=/usr --root="$pkgdir" --optimize=1 --skip-build
}
 
package_python2-setuptools() {
  depends=('python2-packaging')
  provides=('python2-distribute' 'setuptools')
  replaces=('python2-distribute' 'setuptools')

  cd "$srcdir"/setuptools-$pkgver-py2
  python2 setup.py install --prefix=/usr --root="$pkgdir" --optimize=1 --skip-build
  rm "$pkgdir"/usr/bin/easy_install
}
