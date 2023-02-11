# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Andrzej Giniewicz <gginiu@gmail.com>
# Contributor: Johan Förberg <johan@forberg.se>

pkgname=zstd
pkgver=$PKG_VER
pkgrel=$PKG_REL
pkgdesc='Zstandard - Fast real-time compression algorithm'
url='https://facebook.github.io/zstd/'
arch=(x86_64)
license=(BSD GPL2)
depends=(glibc gcc-libs zlib xz lz4)
makedepends=(cmake gtest ninja)
provides=(libzstd.so libzstd.so=1-64)
conflicts=(libzstd.so libzstd.so=1-64)
options=(!debug lto)
source=(https://github.com/facebook/zstd/releases/download/v${pkgver}/zstd-${pkgver}.tar.zst)
sha256sums=('6925880b84aca086308c27036ef1c16e76817372301ead7c37f90e23567f704e')

prepare() {
  cd ${pkgname}-${pkgver}
  # avoid error on tests without static libs, we use LD_LIBRARY_PATH
  sed '/build static library to build tests/d' -i build/cmake/CMakeLists.txt
  sed 's/libzstd_static/libzstd_shared/g' -i build/cmake/tests/CMakeLists.txt
}

build() {
  cd ${pkgname}-${pkgver}
  export OPT_FLAGS="-O3 -flto=auto -flto-compression-level=10 -ffunction-sections -fdata-sections -fgraphite-identity -floop-nest-optimize"
  export CFLAGS=${CFLAGS/-O2/$OPT_FLAGS}
  export CXXFLAGS=${CXXFLAGS/-O2/$OPT_FLAGS}
  export LDFLAGS=${LDFLAGS/-O1/-O3,--gc-sections}

  cmake -S build/cmake -B build -G Ninja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DZSTD_ZLIB_SUPPORT=ON \
    -DZSTD_LZMA_SUPPORT=ON \
    -DZSTD_LZ4_SUPPORT=ON \
    -DZSTD_BUILD_CONTRIB=ON \
    -DZSTD_BUILD_STATIC=OFF \
    -DZSTD_BUILD_TESTS=OFF \
    -DCMAKE_C_FLAGS="$CFLAGS" \
    -DCMAKE_ASM_FLAGS="$CFLAGS" \
    -DCMAKE_CXX_FLAGS="$CXXFLAGS" \
    -DCMAKE_EXE_LINKER_FLAGS="$LDFLAGS" \
    -DCMAKE_MODULE_LINKER_FLAGS="$LDFLAGS" \
    -DCMAKE_SHARED_LINKER_FLAGS="$LDFLAGS" \
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
