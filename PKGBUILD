# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgbase=python-setuptools
pkgname=('python-setuptools' 'python2-setuptools')
pkgver=19.0
pkgrel=1
epoch=1
pkgdesc="Easily download, build, install, upgrade, and uninstall Python packages"
arch=('any')
license=('PSF')
url="http://pypi.python.org/pypi/setuptools"
makedepends=('python-packaging' 'python2-packaging')
checkdepends=('python-mock' 'python2-mock' 'python-pytest-runner' 'python2-pytest-runner' 'git')
source=("https://pypi.python.org/packages/source/s/setuptools/setuptools-${pkgver}.tar.gz")
sha512sums=('83700a0eb4ee6b4aaeae2438fab94c9de31ff93d2d8f86eeefa388edb7464d7d54390ce8cd185f2582714a09dbf7c001aadc27af8628b4cd08c60e9725bf94cb')

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
