# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgbase=python-setuptools
pkgname=('python-setuptools' 'python2-setuptools')
pkgver=12.0.5
pkgrel=1
epoch=1
pkgdesc="Easily download, build, install, upgrade, and uninstall Python packages"
arch=('any')
license=('PSF')
url="http://pypi.python.org/pypi/setuptools"
makedepends=('python' 'python2')
checkdepends=('python-mock' 'python2-mock' 'python-pytest' 'python2-pytest' 'git')
source=("http://pypi.python.org/packages/source/s/setuptools/setuptools-${pkgver}.tar.gz")
sha512sums=('d4d3a0decc9e77abf12ee5121df0cbf9863f9f8cf680a728bf581484587bbd1f1abc523a8ce3b35ab0ffce08d8de8728b450aee8738b67d2046460da4122fb7e')

prepare() {
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
  depends=('python')
  provides=('python-distribute')
  replaces=('python-distribute')

  cd "${srcdir}/setuptools-${pkgver}"
  python3 setup.py install --prefix=/usr --root="${pkgdir}" --optimize=1 --skip-build
}
 
package_python2-setuptools() {
  depends=('python2')
  provides=('python2-distribute' 'setuptools')
  replaces=('python2-distribute' 'setuptools')

  cd "${srcdir}/setuptools-${pkgver}-python2"
  python2 setup.py install --prefix=/usr --root="${pkgdir}" --optimize=1 --skip-build
  rm "${pkgdir}/usr/bin/easy_install"
}
