# Maintainer : bartus <arch-user-repoᘓbartus.33mail.com>
pkgname=luxcorerender
pkgver=2.1
pkgrel=1
pkgdesc="LuxCoreRender is a physically correct, unbiased rendering engine."
arch=('x86_64')
url="https://www.luxcorerender.org/"
license=('Apache')
depends=(openimageio boost blosc embree glfw-x11 gtk3 opencl-icd-loader)
optdepends=("opencl-driver: for gpu acceleration"
            "python-pyside: for pyluxcoretools gui")
makedepends=(git doxygen cmake python-pyside-tools opencl-headers)
conflicts=(luxrays-hg)
provides=(luxrays)
options=('!buildflags')
source=("https://github.com/LuxCoreRender/LuxCore/archive/${pkgname}_v${pkgver}alpha1.tar.gz"
        "python.patch"
        "glfw.patch"
        )
md5sums=('43ec2a57c44681c2ebc308a563d15e60'
         'a1b1594fbb809597759d0573702c06b2'
         '624f2be4cb431f6a4cfcc968d6263ac2')

prepare() {
  cd ${srcdir}/LuxCore-${pkgname}_v${pkgver}alpha1
  msg "python.patch"
  patch -Np1 < ../python.patch
  msg "glfw.patch"
  patch -Np1 < ../glfw.patch
}

build() {
  cd ${srcdir}/LuxCore-${pkgname}_v${pkgver}alpha1
  mkdir -p build && cd build
  cmake ..
  make
}

package() {
  cd ${srcdir}/LuxCore-${pkgname}_v${pkgver}alpha1/build

  install -d -m755 ${pkgdir}/usr/{bin,include,lib}
  install -m755 bin/* ${pkgdir}/usr/bin
  install -m644 lib/* ${pkgdir}/usr/lib
  cp -a ../include ${pkgdir}/usr

  # install pyluxcore to the Python search path
  #  _pypath=`pacman -Ql python | sed -n '/\/usr\/lib\/python[^\/]*\/$/p' | cut -d" " -f 2`
  _pypath=`python -c 'import sys;print("/usr/lib/python{}.{}".format(sys.version_info.major,sys.version_info.minor))'`
  install -d -m755 ${pkgdir}/${_pypath}
  mv ${pkgdir}/usr/lib/pyluxcore.so ${pkgdir}/${_pypath}
}

# vim:set ts=2 sw=2 et:
