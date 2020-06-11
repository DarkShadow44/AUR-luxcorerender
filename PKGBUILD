#!/hint/bash
# Maintainer : bartus <arch-user-repoᘓbartus.33mail.com>

# Configuration
((DISABLE_OPENCL)) || {
  depends+=(opencl-icd-loader)
  makedepends+=(opencl-headers)
  optdepends+=("opencl-driver: for gpu acceleration")
  CMAKE_FLAGS+=("-DLUXRAYS_DISABLE_OPENCL=ON")
}

pkgname=luxcorerender
pkgver=2.2
_name=LuxCore-${pkgname}_v${pkgver}
#_rel="rc1"
[ -n "${_rel}" ] && _pkgver=${pkgver}${_rel} && pkgver+=".${_rel}" || _pkgver=${pkgver}
pkgrel=3
epoch=2
pkgdesc="Physically correct, unbiased rendering engine."
arch=('x86_64')
url="https://www.luxcorerender.org/"
license=('Apache')
depends+=(openimagedenoise openimageio boost-libs blosc embree glfw gtk3)
optdepends+=("pyside2: for pyluxcoretools gui")
makedepends+=(boost git doxygen cmake ninja pyside2-tools)
provides=(luxrays)
source=("https://github.com/LuxCoreRender/LuxCore/archive/${pkgname}_v${_pkgver}.tar.gz"
        "python.patch"
        "glfw.patch"
        "boost016900_serialization.patch"
        )
sha256sums=('35b74e5d3e682dc2826d939c66a1c225354c5eb137c86f10930d1caf70a4629b'
            '0f4e0cb6c5a693c815553cfc25e8a497cf7164b02cca0b4a5351be1b5371bf2e'
            '4e04c3eb653f00d2389aff8e7fda2d244e258cbca3a22e32c13388a3984e4bb1'
            '495d183aef045e53ec8c53aa08cdcc082fb4e69ccb0857693cb0cf2684db0760')

prepare() {
  for patch in "${srcdir}"/*.patch; do
    msg2  "apply $patch..."
    patch -Np1 -d "${srcdir}"/${_name} -i "$patch"
  done
}

build() {
  _pyver=$(python -c "from sys import version_info; print(\"%d%d\" % (version_info.major,version_info.minor))")
  CMAKE_FLAGS+=("-DPYTHON_V=${_pyver}")
  cmake "${CMAKE_FLAGS[@]}" -S "${srcdir}"/${_name} -B build -G Ninja
  ninja "$(grep -oP -- '-+[A-z]+ ?[0-9]*'<<<"${MAKEFLAGS:--j1}")" -C "${srcdir}/build"
}

package() {
  cd "${srcdir}"/build

  install -d -m755 "${pkgdir}"/usr/{bin,include,lib}
  install -m755 bin/* "${pkgdir}"/usr/bin
  install -m644 lib/* "${pkgdir}"/usr/lib
  cp -a "${srcdir}"/${_name}/include "${pkgdir}"/usr
  for file in "${pkgdir}"/usr/include/*/*.in; do mv "$file" "${file%.in}"; done

  # install pyluxcore to the Python search path
  #  _pypath=`pacman -Ql python | sed -n '/\/usr\/lib\/python[^\/]*\/$/p' | cut -d" " -f 2`
  _pypath=$(python -c 'from sys import version_info;print("/usr/lib/python{}.{}".format(version_info.major,version_info.minor))')
  install -d -m755 "${pkgdir}/${_pypath}"
  mv "${pkgdir}"/usr/lib/pyluxcore.so "${pkgdir}/${_pypath}"
}
# vim:set ts=2 sw=2 et:
