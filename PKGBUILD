# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgbase=python-setuptools
pkgname=('python-setuptools' 'python2-setuptools')
pkgver=18.8
pkgrel=1
epoch=1
pkgdesc="Easily download, build, install, upgrade, and uninstall Python packages"
arch=('any')
license=('PSF')
url="http://pypi.python.org/pypi/setuptools"
makedepends=('python-packaging' 'python2-packaging')
checkdepends=('python-mock' 'python2-mock' 'python-pytest-runner' 'python2-pytest-runner' 'git')
source=("https://pypi.python.org/packages/source/s/setuptools/setuptools-${pkgver}.tar.gz")
sha512sums=('718f8ef19e639450f62e1751715bac65ce2bd218b81614d07c9283a01be3a085ec5422ff9d96a54e74c05135a1a4bb167ece6d4ecece588716d05982c9f25811')

prepare() {
  # We ship separated python{,2}-packaging packages.
  rm -r setuptools-${pkgver}/pkg_resources/_vendor

  cp -a setuptools-${pkgver}{,-python2}

  cd "${srcdir}"/setuptools-${pkgver}
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python3|" setuptools/command/easy_install.py

  cd "${srcdir}"/setuptools-${pkgver}-python2
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
  LC_CTYPE=en_US.utf8 python3 setup.py ptr

  # Check python2 module
  cd "${srcdir}"/setuptools-${pkgver}-python2
  LC_CTYPE=en_US.utf8 python2 setup.py ptr
}
 
package_python-setuptools() {
  depends=('python-packaging')
  provides=('python-distribute')
  replaces=('python-distribute')

  cd "${srcdir}/setuptools-${pkgver}"
  python3 setup.py install --prefix=/usr --root="${pkgdir}" --optimize=1 --skip-build
}
 
package_python2-setuptools() {
  depends=('python2-packaging')
  provides=('python2-distribute' 'setuptools')
  replaces=('python2-distribute' 'setuptools')

  cd "${srcdir}/setuptools-${pkgver}-python2"
  python2 setup.py install --prefix=/usr --root="${pkgdir}" --optimize=1 --skip-build
  rm "${pkgdir}/usr/bin/easy_install"
}
