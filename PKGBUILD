# Maintainer: Angel Velasquez <angvp@archlinux.org>
# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgbase=python-setuptools
pkgname=('python-setuptools' 'python2-setuptools')
pkgver=34.0.1
pkgrel=1
epoch=1
pkgdesc="Easily download, build, install, upgrade, and uninstall Python packages"
arch=('any')
license=('PSF')
url="http://pypi.python.org/pypi/setuptools"
makedepends=('python-packaging' 'python2-packaging' 'python-appdirs' 'python2-appdirs' 'python-pip'
             'python2-pip' 'git')
checkdepends=('python-pytest-runner' 'python2-pytest-runner' 'python-pytest-flake8'
              'python2-pytest-flake8' 'python-mock' 'python2-mock' 'git')
source=("git+https://github.com/pypa/setuptools.git#tag=v$pkgver")
sha512sums=('SKIP')

prepare() {
  # Don't download and install deps
  sed -i '/pip.main(args)/d' setuptools/bootstrap.py

  # Remove post-release tag since we are using stable tags
  sed -e '/tag_build = .post/d' \
      -e '/tag_date = 1/d' \
      -i setuptools/setup.cfg

  cp -a setuptools{,-py2}

  cd "$srcdir"/setuptools
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python3|" setuptools/command/easy_install.py

  cd "$srcdir"/setuptools-py2
  sed -i -e "s|^#\!.*/usr/bin/env python|#!/usr/bin/env python2|" setuptools/command/easy_install.py
  sed -i -e "s|'pip'|'pip2'|" setuptools/tests/{test_develop.py,test_namespaces.py}

  export SETUPTOOLS_INSTALL_WINDOWS_SPECIFIC_FILES=0
}
    
build() {
  cd "$srcdir"/setuptools
  python bootstrap.py
  python setup.py build

  cd "$srcdir"/setuptools-py2
  python2 bootstrap.py
  python2 setup.py build
}

check() { (
  # Workaround UTF-8 tests by setting LC_CTYPE
  export LC_CTYPE=en_US.UTF-8

  # https://github.com/pypa/setuptools/pull/810
  export PYTHONDONTWRITEBYTECODE=1

  cd "$srcdir"/setuptools
  python setup.py ptr

  cd "$srcdir"/setuptools-py2
  python2 setup.py ptr
)}
 
package_python-setuptools() {
  depends=('python-packaging' 'python-appdirs')
  provides=('python-distribute')
  replaces=('python-distribute')

  cd "$srcdir"/setuptools
  python setup.py install --prefix=/usr --root="$pkgdir" --optimize=1 --skip-build
}
 
package_python2-setuptools() {
  depends=('python2-packaging' 'python2-appdirs')
  provides=('python2-distribute')
  replaces=('python2-distribute')

  cd "$srcdir"/setuptools-py2
  python2 setup.py install --prefix=/usr --root="$pkgdir" --optimize=1 --skip-build
  rm "$pkgdir"/usr/bin/easy_install
}
