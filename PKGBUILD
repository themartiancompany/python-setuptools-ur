# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@gmail.com>

pkgbase=python-setuptools
pkgname=('python-setuptools' 'python2-setuptools')
pkgver=5.0.2
pkgrel=1
epoch=1
pkgdesc="Easily download, build, install, upgrade, and uninstall Python packages"
arch=('any')
license=('PSF')
url="http://pypi.python.org/pypi/setuptools"
makedepends=('python' 'python2')
checkdepends=('python-pytest' 'python2-pytest')
source=("http://pypi.python.org/packages/source/s/setuptools/setuptools-${pkgver}.tar.gz")
sha512sums=('c31a9c2e29491e8f11fe6e1f5eaaedb6f4c7e222b8e46e59b01b7334847140bece821f6467450afb2141c15928548d59787bcdcba4c2a78982888fde1af68595')

prepare() {
  cp -a setuptools-${pkgver}{,-python2}

  cd "${srcdir}"/setuptools-${pkgver}
  sed -i -e "s|^#\!.*/usr/bin/python|#!/usr/bin/python3|" setuptools/tests/test_resources.py
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python3|" setuptools/command/easy_install.py

  cd "${srcdir}"/setuptools-${pkgver}-python2
  sed -i -e "s|^#\!.*/usr/bin/python|#!/usr/bin/python2|" setuptools/tests/test_resources.py
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python2|" setuptools/command/easy_install.py
}

build() {
  # Build python 3 module
  cd "${srcdir}"/setuptools-${pkgver}
  python3 setup.py build

  # Build python 2 module
  cd "${srcdir}"/setuptools-${pkgver}-python2
  python2 setup.py build
}

check() {
  # Workaround UTF-8 tests by setting LC_CTYPE

  # Check python3 module
  cd "${srcdir}"/setuptools-${pkgver}
  LC_CTYPE=en_US.utf8 python3 setup.py test

  # Check python2 module
  cd "${srcdir}"/setuptools-${pkgver}-python2
  LC_CTYPE=en_US.utf8 python2 setup.py test
}
 
package_python-setuptools() {
  depends=('python>=3.4')
  provides=('python-distribute')
  replaces=('python-distribute')

  cd "${srcdir}/setuptools-${pkgver}"
  python3 setup.py install --prefix=/usr --root="${pkgdir}" --optimize=1 --skip-build
}
 
package_python2-setuptools() {
  depends=('python2>=2.7')
  provides=('python2-distribute' 'setuptools')
  replaces=('python2-distribute' 'setuptools')

  cd "${srcdir}/setuptools-${pkgver}-python2"
  python2 setup.py install --prefix=/usr --root="${pkgdir}" --optimize=1 --skip-build
  rm "${pkgdir}/usr/bin/easy_install"
}
