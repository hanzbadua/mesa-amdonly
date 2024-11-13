# Maintainer: Reza Jahanbakhshi <reza.jahanbakhshi at gmail dot com
# Contributor: Lone_Wolf <lone_wolf@klaas-de-kat.nl>
# Contributor: Armin K. <krejzi at email dot com>
# Contributor: Kristian Klausen <klausenbusk@hotmail.com>
# Contributor: Egon Ashrafinia <e.ashrafinia@gmail.com>
# Contributor: Tavian Barnes <tavianator@gmail.com>
# Contributor: Jan de Groot <jgc@archlinux.org>
# Contributor: Andreas Radke <andyrtr@archlinux.org>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: Antti "Tera" Oja <antti.bofh@gmail.com>
# Contributor: Diego Jose <diegoxter1006@gmail.com>
# mesa-amdonly changes: Hanz Badua <hanzbadua at icloud dot com>

pkgname=mesa-amdonly
pkgdesc="an open-source implementation of the OpenGL specification (mesa-amdonly)"
pkgver=0
pkgrel=1
arch=('x86_64')
makedepends=(
    'cbindgen'
    'clang'
    'cmake'
    'directx-headers'
    'elfutils'
    'expat'
    'gcc-libs'
    'glibc'
    'glslang'
    'libclc'
    'libdrm'
    'libelf'
    'libglvnd'
    'libomxil-bellagio'
    'libva'
    'libvdpau'
    'libx11'
    'libxcb'
    'libxext'
    'libxfixes'
    'libxml2'
    'libxrandr'
    'libxshmfence'
    'libxxf86vm'
    'llvm'
    'llvm-libs'
    'lm_sensors'
    'meson'
    'python-mako'
    'python-packaging'
    'python-ply'
    'rust'
    'rust-bindgen'
    'spirv-llvm-translator'
    'spirv-tools'
    'systemd-libs'
    'vulkan-icd-loader'
    'wayland'
    'wayland-protocols'
    'xcb-util-keysyms'
    'xorgproto'
    'zlib'
    'zstd'
)
depends=(
    'expat'
    'gcc-libs'
    'glibc'
    'libdrm'
    'libelf'
    'libglvnd'
    'libomxil-bellagio'
    'libx11'
    'libxcb'
    'libxext'
    'libxfixes'
    'libxshmfence'
    'libxxf86vm'
    'llvm-libs'
    'lm_sensors'
    'wayland'
    'zlib'
    'zstd'
)
optdepends=('opengl-man-pages: for the OpenGL API man pages' 'clang: opencl' 'compiler-rt: opencl')
provides=(
    'vulkan-mesa-layers'
    'opencl-rusticl-mesa'
    'opencl-driver'
    'opengl-driver'
    'vulkan-driver'
    'vulkan-radeon'
    'vulkan-swrast'
    'libva-mesa-driver'
    'mesa-vdpau'
    'mesa-libgl'
    'mesa'
)
conflicts=(
    'vulkan-mesa-layers'
    'opencl-rusticl-mesa'
    'vulkan-radeon'
    'vulkan-swrast'
    'libva-mesa-driver'
    'mesa-vdpau'
    'mesa-libgl'
    'mesa'
)
url="https://www.mesa3d.org"
license=('custom')
source=('mesa::git+https://gitlab.freedesktop.org/mesa/mesa.git#branch=main' 'LICENSE')
sha256sums=('SKIP' 'SKIP')
options=(!lto !debug)

pkgver() {
    cd mesa
    local _ver
    _ver=$(<VERSION)

    local _patchver
    local _patchfile
    for _patchfile in "${source[@]}"; do
        _patchfile="${_patchfile%%::*}"
        _patchfile="${_patchfile##*/}"
        [[ $_patchfile = *.patch ]] || continue
        _patchver="${_patchver}$(md5sum ${srcdir}/${_patchfile} | cut -c1-32)"
    done
    _patchver="$(echo -n $_patchver | md5sum | cut -c1-7)"

    echo ${_ver/-/_}.$(git rev-list --count HEAD).$(git rev-parse --short HEAD).${_patchver}
}

prepare() {
    # although removing _build folder in build() function feels more natural,
    # that interferes with the spirit of makepkg --noextract
    if [  -d _build ]; then
        rm -rf _build
    fi

    local _patchfile
    for _patchfile in "${source[@]}"; do
        _patchfile="${_patchfile%%::*}"
        _patchfile="${_patchfile##*/}"
        [[ $_patchfile = *.patch ]] || continue
        echo "Applying patch $_patchfile..."
        patch --directory=mesa --forward --strip=1 --input="${srcdir}/${_patchfile}"
    done
}

build() {
    local meson_options=(
        -D android-strict=false
        -D android-libbacktrace=disabled
        -D c_args="-O3 -g0 -march=native"
        -D c_link_args="-O3 -g0 -march=native"
        -D cpp_args="-O3 -g0 -march=native"
        -D cpp_link_args="-O3 -g0 -march=native"
        -D rust_args=""
        -D rust_link_args=""
        -D b_asneeded=false
        -D b_ndebug=true
        -D b_pch=false
        -D b_lto=false
        -D buildtype=release
        -D egl=enabled
        -D gallium-drivers=radeonsi,softpipe,llvmpipe,zink
        -D gallium-extra-hud=true
        -D gallium-nine=true
        -D gallium-opencl=disabled
        -D gallium-rusticl=true
        -D gallium-va=enabled
        -D gallium-vdpau=enabled
        -D gallium-xa=disabled
        -D gbm=enabled
        -D gles1=disabled
        -D gles2=enabled
        -D glvnd=enabled
        -D glx=dri
        -D intel-clc=enabled
        -D intel-rt=disabled
        -D libunwind=enabled
        -D llvm=enabled
        -D lmsensors=enabled
        -D microsoft-clc=disabled
        -D osmesa=true
        -D platforms=wayland
        -D rust_std=2021
        -D shader-cache=enabled
        -D shared-glapi=enabled
        -D strip=true
        -D valgrind=disabled
        -D video-codecs=all
        -D vulkan-beta=true
        -D vulkan-drivers=amd,swrast
        -D vulkan-layers=device-select,overlay
        -D tools=[]
        -D zstd=enabled
        --wrap-mode=nofallback
        --force-fallback-for=syn,paste
        -D prefix=/usr
        -D sysconfdir=/etc
    )

    meson setup mesa _build "${meson_options[@]}"
    meson configure --no-pager _build
    ninja $NINJAFLAGS -C _build
}

package() {
    DESTDIR="${pkgdir}" ninja $NINJAFLAGS -C _build install

    # remove script file from /usr/bin
    # https://gitlab.freedesktop.org/mesa/mesa/issues/2230
    rm "${pkgdir}/usr/bin/mesa-overlay-control.py"
    rmdir "${pkgdir}/usr/bin"

    # indirect rendering
    ln -s /usr/lib/libGLX_mesa.so.0 "${pkgdir}/usr/lib/libGLX_indirect.so.0"
 
    install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" "${srcdir}/LICENSE"
}
