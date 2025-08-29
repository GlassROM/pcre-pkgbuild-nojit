# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Mateusz 'mrlemux' Lemusisk mrlemux at gmail dotcom
# Based on the pcre package by Sébastien "Seblu" Luttringer
# Contributor: Allan McRae <allan@archlinux.org>
# Contributor: Eric Belanger <eric@archlinux.org>
# Contributor: John Proctor <jproctor@prium.net>

pkgname=pcre2
pkgver=10.46
pkgrel=1
pkgdesc='A library that implements Perl 5-style regular expressions. 2nd version'
arch=(x86_64)
url='https://github.com/PCRE2Project/pcre2'
license=(
  BSD-2-Clause
  'BSD-3-Clause WITH PCRE2-exception'
)
depends=(
  bzip2
  glibc
  readline
  zlib
)
makedepends=(git)
optdepends=('sh: for pcre2-config')
provides=(libpcre2-{8,16,32,posix}.so)
options=(staticlibs)
source=(
  $pkgname::git+$url?signed#tag=$pkgname-$pkgver
)
sha512sums=('0adb31a8db7fbe33af3155ffccc117cbf604ac290fbbc5087638497dd7d22ab30b493dad7a0d9d3532980cf56ca76fc992b0e279d8ff641e9f9a31a1e28ef9a8')
b2sums=('499d16dc44df9f2d352e3f954f576369cc7a11018e46d96ec24f33ffba6eec55b670fd5437b02a013db1c8f63612ebfaafb87a2d762a1f9c8ba271ef3727f1d5')
validpgpkeys=(
  45F68D54BBE23FB3039B46E59766E084FB0F43D8  # Philip Hazel <ph10@hermes.cam.ac.uk>
  A95536204A3BB489715231282A98E77EB6F24CA8  # Nicholas Wilson <nicholas@nicholaswilson.me.uk>
)

prepare() {
  cd $pkgname

  ./autogen.sh

  # extract licenses
  sed -n '70,94p' LICENCE.md > ../BSD-3-Clause.txt
  sed -n '100,104p' LICENCE.md > ../PCRE2-exception.txt
}

build() {
  local configure_options=(
    --disable-jit
    --enable-pcre2-16
    --enable-pcre2-32
    --enable-pcre2grep-libbz2
    --enable-pcre2grep-libz
    --enable-pcre2test-libreadline
    --prefix=/usr
  )

  cd $pkgname

  # use fat LTO objects for static libraries
  CFLAGS+=" -ffat-lto-objects"
  CXXFLAGS+=" -ffat-lto-objects"

export CC=clang
    export CXX=clang++
    export LD=ld.lld
    export AR=llvm-ar
    export NM=llvm-nm
    export RANLIB=llvm-ranlib
    export STRIP=llvm-strip
    export OBJDUMP=llvm-objdump
    export OBJCOPY=llvm-objcopy
    export READELF=llvm-readelf

export LDFLAGS+=" -Wl,-O1 -Wl,--sort-common -Wl,--as-needed -Wl,-z,relro -Wl,-z,now -Wl,-z,pack-relative-relocs -flto=auto -Wl,-O3 -Wl,-z,noexecstack -Wl,--strip-all -Wl,--sort-common -Wl,--no-undefined -Wl,-z,now -Wl,-z,relro -Wl,-O3,--as-needed,-z,defs,-z,relro,-z,now,-z,nodlopen,-z,text -Wl,-z,pack-relative-relocs -Wl,-z,shstk -Wl,-z,retpolineplt -Wl,--fatal-warnings,--warn-unresolved-symbols,--no-undefined -Wl,-Bsymbolic-functions -flto -Wl,--gc-sections"
export CXXFLAGS+=" -march=x86-64 -mtune=generic -O2 -pipe -fno-plt -fexceptions -Wp,-D_FORTIFY_SOURCE=3 -Wformat -Werror=format-security -fstack-clash-protection -fcf-protection -fno-omit-frame-pointer -mno-omit-leaf-frame-pointer -Wp,-D_GLIBCXX_ASSERTIONS -g -flto=auto -fno-plt -fuse-ld=lld -fomit-frame-pointer -fPIC -ftrivial-auto-var-init=zero -flto -fcf-protection -D_FORTIFY_SOURCE=3 -fwrapv -fzero-call-used-regs=all -fno-delete-null-pointer-checks -D_GLIBCXX_ASSERTIONS -g0 -fPIC -fno-strict-overflow -fno-strict-aliasing -fvisibility=hidden -fsanitize=cfi-cast-strict,cfi-derived-cast,cfi-unrelated-cast -fsanitize-cfi-icall-generalize-pointers -fsanitize-cfi-icall-experimental-normalize-integers -fsanitize-trap=all -fstack-protector-all -fstack-clash-protection -fvisibility-inlines-hidden -Wno-unused-command-line-argument -Wno-error=unused-command-line-argument -flto -DTCP_FASTOPEN=23 -fsanitize=cfi -O3 -funroll-loops -fdata-sections -ffunction-sections -fstrict-flex-arrays=3 -fPIE -pie -fsanitize=undefined,bounds,integer"
export CFLAGS+=" -march=x86-64 -mtune=generic -O2 -pipe -fno-plt -fexceptions -Wp,-D_FORTIFY_SOURCE=3 -Wformat -Werror=format-security -fstack-clash-protection -fcf-protection -fno-omit-frame-pointer -mno-omit-leaf-frame-pointer -g -flto=auto -fno-plt -fuse-ld=lld -fPIC -fomit-frame-pointer -g0 -fPIC -fno-strict-overflow -fno-strict-aliasing -fvisibility=hidden -fsanitize=cfi-cast-strict,cfi-derived-cast,cfi-unrelated-cast -fsanitize-cfi-icall-generalize-pointers -fsanitize-cfi-icall-experimental-normalize-integers -fsanitize-trap=all -ftrivial-auto-var-init=zero -fcf-protection=full -fstack-protector-all -fstack-clash-protection -D_FORTIFY_SOURCE=3 -fwrapv -fzero-call-used-regs=all -fno-delete-null-pointer-checks -Wno-unused-command-line-argument -Wno-error=unused-command-line-argument -flto -fsanitize=cfi -DTCP_FASTOPEN=23 -O3 -funroll-loops -fdata-sections -ffunction-sections -fstrict-flex-arrays=3 -fsanitize=undefined,bounds,integer"

  ./configure "${configure_options[@]}"
  make -j$(nproc --all)
}

package() {
  make DESTDIR="$pkgdir" install -C $pkgname

  install -Dm644 ./*.txt -t "$pkgdir/usr/share/licenses/$pkgname/"
}

# vim:set sw=2 sts=-1 et:
