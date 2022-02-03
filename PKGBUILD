# Maintainer: Jerry Matjila <jerry.matjila@gmail.com>
# Creater: Jerry Matjila <jerry.matjila@gmail.com>
pkgname=flashlight
pkgbase=flashlight-cuda
pkgver=r1186.24199f5f
pkgrel=1
pkgdesc="A C++ standalone library for machine learning"
license=('MIT')
arch=('x86_64')
url="https://github.com/flashlight/flashlight"
depends=('arrayfire' 'cuda' 'cudnn' 'intel-mkl' 'onednn' 'nccl' 'google-glog' 'gflags' 'libsndfile')
makedepends=('cmake' 'git' 'gtest' 'doxygen' 'python-sphinx' 'python-breathe' 'python-sphinx_rtd_theme')
conflicts=('flashlight' 'flashlight-cpu')
source=(git+$url.git
        ${pkgbase}.conf
        "flashlight_enabling_innovation.pdf"
        "wave2letter++.pdf"
)
sha256sums=('SKIP'
            '1fe72df591fd51cb1efb50d455cdb46a7d904bed8a722a878710cfc503151f43'
            '44ba6ada030aa2cc2ca52cfe345297c830300a4f0197f381293f8479e88c080d'
            'cbb06491db281c5a062de580cac14994ae1a1314d4aa594500731ce84040f774')

prepare() {
    cd $pkgname
    git submodule update --init
}

pkgver() {
    cd "$pkgname"
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
    # dependency package fftw is causing a problem for building speech therefore is it off: https://github.com/FFTW/fftw3/issues/235 
    cmake -B build -S "$pkgname" \
    -DCMAKE_SKIP_INSTALL_RPATH=YES \
    -DCMAKE_BUILD_TYPE=RELEASE  \
    -DCMAKE_INSTALL_PREFIX="/opt/$pkgbase" \
    -DFL_BACKEND=CUDA \
    -DFL_BUILD_STANDALONE=ON \
    -DFL_BUILD_ALL_LIBS=ON \
    -DFL_BUILD_LIB_AUDIO=OFF \
    -DFL_BUILD_ALL_PKGS=ON \
    -DFL_BUILD_PKG_SPEECH=OFF \
    -DFL_BUILD_APP_IMGCLASS=ON \
    -Wno-dev

    cmake --build build -- -j $(nproc)
  
    cd "$pkgname/docs" && doxygen && make html
}

check() {
    cd build
    ctest --output-on-failure
}

package() {
    DESTDIR="$pkgdir" cmake --install build --strip
    
    doc_dir="$pkgdir/usr/share/doc/$pkgname"
    mkdir -p "$doc_dir"
    rsync -av --delete --no-o --no-g --chmod=D755,F644 "$pkgname/docs/build/html" "$doc_dir" 
    
    install -Dm644 "${pkgbase}.conf" "${pkgdir}/etc/ld.so.conf.d/${pkgbase}.conf"
    install -Dm644 "flashlight_enabling_innovation.pdf" "$doc_dir"
    install -Dm644 "wave2letter++.pdf" "$doc_dir"
}

