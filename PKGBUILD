# $Id$
# Maintainer: Alexander F. Rødseth <xyproto@archlinux.org>
# Maintainer: Lex Black <autumn-wind at web dot de>
# Contributor: Michael Jakl <jakl.michael@gmail.com>
# Contributor: devmotion <nospam-archlinux.org@devmotion.de>
# Contributor: Valentin Churavy <v.churavy@gmail.com>
# With contributions from many kind people at https://aur.archlinux.org/packages/julia-git/

_pkgbase=julia
pkgbase=julia-git
pkgname=('julia-git' 'julia-git-docs')
pkgver=0.7.0.beta2.r42684.g6476f51886
_pkgver=0.7.0
pkgrel=1
arch=('x86_64')
pkgdesc='High-level, high-performance, dynamic programming language'
url='https://julialang.org/'
license=('MIT')
depends=('fftw' 'hicolor-icon-theme' 'libgit2>=0.23' 'libunwind' 'libutf8proc>=2.1' 'mpfr>=4.0' 'pcre2>=10.00' 'suitesparse>=4.1')
makedepends=('chrpath' 'cmake' 'gcc-fortran' 'gmp>=5.0' 'gtk-update-icon-cache' 'python2')
# Needed if building the documentation
#makedepends+=('juliadoc-git' 'texlive-langcjk' 'texlive-latexextra')
options=('!emptydirs' 'staticlibs')
source=("git://github.com/JuliaLang/julia.git#branch=master"
        'julia-libunwind-version.patch'
        'julia-makefile.patch')
sha256sums=('SKIP'
            '967cac3f9c3d1f41a027dd2c914048c1077dec79cfdc4e9a69f356b7552e6709'
            '29b68ecdaf91770585609f54e5559e571d6b57a6312a3e8335caa7d647a2d2ca')

pkgver() {
  cd $_pkgbase

  # use the version from VERSION file
  ver=`git show makepkg:VERSION | sed 's/-/./g'`
  # Combine ver with rev-count and latest commit
  printf "%s.r%s.g%s" $(echo $ver) "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
  cd $_pkgbase
  git submodule init
  git submodule update

  patch -p1 -i ../julia-libunwind-version.patch
  patch -p0 -i ../julia-makefile.patch # make 'make install' really just install
}

build() {
  #
  # See FS#57387 for why USE_SYSTEM_LLVM=0 is used, for now
  # See FS#58221 for why USE_SYSTEM_ARPACK=0 is used, for now
  #
  # patchelf is not even used unless $(private_libdir_rel) != $(build_private_libdir_rel)
  # but we USE_SYSTEM_PATCHELF=1 to prevent building it. This is why it is not in makedepends.
  #
  export CFLAGS="$CFLAGS -w"
  export CXXFLAGS="$CXXFLAGS -w"
  make -C "$_pkgbase" \
    prefix=/usr \
    sysconfdir=/etc \
    MARCH=x86-64 \
    JULIA_BUILD_MODE=release \
    USE_BLAS64=0 \
    USE_INTEL_MKL=0 \
    USE_LLVM_SHLIB=1 \
    USE_SYSTEM_ARPACK=0 \
    USE_SYSTEM_BLAS=0 \
    USE_SYSTEM_DSFMT=0 \
    USE_SYSTEM_FFTW=1 \
    USE_SYSTEM_GMP=1 \
    USE_SYSTEM_LAPACK=0 \
    USE_SYSTEM_LIBGIT2=1 \
    USE_SYSTEM_LIBM=0 \
    USE_SYSTEM_LIBUNWIND=1 \
    USE_SYSTEM_LIBUV=0 \
    USE_SYSTEM_LLVM=0 \
    USE_SYSTEM_MPFR=1 \
    USE_SYSTEM_OPENLIBM=0 \
    USE_SYSTEM_OPENSPECFUN=0 \
    USE_SYSTEM_PATCHELF=1 \
    USE_SYSTEM_PCRE=1 \
    USE_SYSTEM_SUITESPARSE=1 \
    USE_SYSTEM_UTF8PROC=1 \
    default docsindex
}

package_julia-git() {
  provides=('julia')
  conflicts=('julia')
  backup=('etc/julia/juliarc.jl')
  optdepends=('gnuplot: If using the Gaston Package from julia')

  export CFLAGS="$CFLAGS -w"
  export CXXFLAGS="$CXXFLAGS -w"
  make -C "$_pkgbase" \
    DESTDIR="$pkgdir" \
    prefix=/usr \
    sysconfdir=/etc \
    MARCH=x86-64 \
    USE_BLAS64=0 \
    USE_INTEL_MKL=0 \
    USE_LLVM_SHLIB=1 \
    USE_SYSTEM_ARPACK=0 \
    USE_SYSTEM_BLAS=0 \
    USE_SYSTEM_DSFMT=0 \
    USE_SYSTEM_FFTW=1 \
    USE_SYSTEM_GMP=1 \
    USE_SYSTEM_LAPACK=0 \
    USE_SYSTEM_LIBGIT2=1 \
    USE_SYSTEM_LIBM=0 \
    USE_SYSTEM_LIBUNWIND=1 \
    USE_SYSTEM_LIBUV=0 \
    USE_SYSTEM_LLVM=0 \
    USE_SYSTEM_MPFR=1 \
    USE_SYSTEM_OPENLIBM=0 \
    USE_SYSTEM_OPENSPECFUN=0 \
    USE_SYSTEM_PATCHELF=1 \
    USE_SYSTEM_PCRE=1 \
    USE_SYSTEM_SUITESPARSE=1 \
    USE_SYSTEM_UTF8PROC=1 \
    install

  # Remove duplicate man-page from julia/doc
  rm -rf "$pkgdir/usr/share/$_pkgbase/doc/man"

  # FS#58211 && https://github.com/JuliaLang/julia/issues/26830
  chrpath -r '$ORIGIN/julia' "$pkgdir/usr/lib/libjulia.so.$_pkgver"
  # points to /usr/lib
  chrpath -d "$pkgdir/usr/bin/julia"

  # Documentation is in the julia-docs package
  rm -rf "$pkgdir/usr/share/"{doc,julia/doc}

  # License
  install -Dm644 "$_pkgbase/LICENSE.md" "$pkgdir/usr/share/licenses/$pkgname/LICENSE.md"
}

package_julia-git-docs() {
  pkgdesc='Documentation for Julia'
  depends=('julia')
  provides=('julia-docs')
  conflicts=('julia-docs')

  cd "$_pkgbase"

  install -d "$pkgdir/usr/share/doc"
  cp -r doc/_build "$pkgdir/usr/share/doc/$_pkgbase"
  rm -rf "$pkgdir"/usr/share/doc/$_pkgbase/man/
  install -Dm644 LICENSE.md "$pkgdir/usr/share/licenses/$pkgname/LICENSE.md"
}

# vim: ts=2 sw=2 et:
