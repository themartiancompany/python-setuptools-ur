# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@gmail.com>

pkgbase=python-setuptools
pkgname=('python-setuptools' 'python2-setuptools')
pkgver=2.2
pkgrel=1
pkgdesc="Easily download, build, install, upgrade, and uninstall Python packages"
arch=('any')
license=('PSF')
url="http://pypi.python.org/pypi/setuptools"
makedepends=('python' 'python2')
source=("http://pypi.python.org/packages/source/s/setuptools/setuptools-${pkgver}.tar.gz")
md5sums=('04a7664538957b832710653fd7d5b4e6')

#check() {
#   # Check python3 module
#   cd "${srcdir}"/setuptools-${pkgver}
#   python3 setup.py test
#
#   # Check python2 module
#   cd "${srcdir}"/setuptools-${pkgver}-python2
#   python2 setup.py test
#}
 
prepare() {
   cd "${srcdir}"
 
   pushd setuptools-${pkgver}
   popd
 
   cp -a setuptools-${pkgver}{,-python2}
 
   cd "${srcdir}"/setuptools-${pkgver}
   sed -i -e "s|^#\!.*/usr/bin/python|#!/usr/bin/python3|" setuptools/tests/test_resources.py
   sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python3|" setuptools/command/easy_install.py

   cd ../setuptools-${pkgver}-python2
   sed -i -e "s|^#\!.*/usr/bin/python|#!/usr/bin/python2|" setuptools/tests/test_resources.py
   sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python2|" setuptools/command/easy_install.py
}

build() {
   # Build python 3 module
   cd "${srcdir}"/setuptools-${pkgver}
   python3 setup.py build
 
   # Build python 2 module
   cd ../setuptools-${pkgver}-python2
   python2 setup.py build
}
 
package_python-setuptools() {
   depends=('python>=3.3')
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
