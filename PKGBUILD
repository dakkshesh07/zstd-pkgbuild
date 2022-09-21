# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Andrzej Giniewicz <gginiu@gmail.com>
# Contributor: Johan Förberg <johan@forberg.se>

pkgname=zstd
pkgver=1.5.2
pkgrel=7
pkgdesc='Zstandard - Fast real-time compression algorithm'
url='https://facebook.github.io/zstd/'
arch=(x86_64)
license=(BSD GPL2)
depends=(glibc gcc-libs zlib xz lz4)
makedepends=(cmake gtest ninja)
provides=(libzstd.so)
options=(!debug)
source=(https://github.com/facebook/zstd/releases/download/v${pkgver}/zstd-${pkgver}.tar.zst)
sha256sums=('3ea06164971edec7caa2045a1932d757c1815858e4c2b68c7ef812647535c23f')
b2sums=('513e4526a92bcb59416b3457d186a30e554f9e0cf21d7114eb3e9fbcbd9d662c8d95cf0b06237f6fe3f756862c63de0aa146d6a23cb4111c16e6459608d115f1')

prepare() {
  cd ${pkgname}-${pkgver}
  # avoid error on tests without static libs, we use LD_LIBRARY_PATH
  sed '/build static library to build tests/d' -i build/cmake/CMakeLists.txt
  sed 's/libzstd_static/libzstd_shared/g' -i build/cmake/tests/CMakeLists.txt
  patch -p1 <(curl -s https://github.com/facebook/zstd/commit/eceecc5b2cade40e2ffe7e4ff4c7d2e16883961a.patch)
  patch -p1 <(curl -s https://github.com/facebook/zstd/commit/f92ec5ea54d64207f5ffd30b5c746367a6e75dc4.patch)
  patch -p1 <(curl -s https://github.com/facebook/zstd/commit/7a18d709ae5a8ba53c5199adb2e8461cb216fb00.patch)
  patch -p1 <(curl -s https://github.com/facebook/zstd/commit/03903f57012054852c0c26daca7131a130bb5cbf.patch)
  patch -p1 <(curl -s https://github.com/facebook/zstd/commit/9e1b4828e56a028efa0efdd7c30a58e6dd48c8c1.patch)
  patch -p1 <(curl -s https://github.com/facebook/zstd/commit/9e1b4828e56a028efa0efdd7c30a58e6dd48c8c1.patch)
}

build() {
  cd ${pkgname}-${pkgver}
  export CFLAGS+=' -ffat-lto-objects'
  export CXXFLAGS+=' -ffat-lto-objects'

  cmake -S build/cmake -B build -G Ninja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_INSTALL_LIBDIR=lib \
    -DZSTD_BUILD_CONTRIB=ON \
    -DZSTD_BUILD_STATIC=OFF \
    -DZSTD_BUILD_TESTS=OFF \
    -DZSTD_PROGRAMS_LINK_SHARED=ON
  cmake --build build
}

check() {
  cd ${pkgname}-${pkgver}
  export LD_LIBRARY_PATH="$(pwd)/build/lib"
  ctest -VV --test-dir build
}

package() {
  cd ${pkgname}-${pkgver}
  DESTDIR="${pkgdir}" cmake --install build
  ln -sf /usr/bin/zstd "${pkgdir}/usr/bin/zstdmt"
  install -Dm 644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}"
}

# vim: ts=2 sw=2 et:
