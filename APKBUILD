_gitref="f53572f7c8df7e58fbde91caaf38dfe2bb1f3c3a"
pkgname=openssl-chacha
pkgver=1.0.2e
pkgrel=0
pkgdesc="Toolkit for SSL v2/v3 and TLS v1 built with chacha"
url="https://github.com/PeterMosmans/openssl"
depends=
makedepends_build="perl"
makedepends_host="zlib-dev"
makedepends="$makedepends_host $makedepends_build"
depends_dev="zlib-dev"
arch="all"
license="openssl"
replaces="openssl openssl-dev openssl-doc libcrypto1.0 libssl1.0"

subpackages="${pkgname}-dev ${pkgname}-doc libcrypto-chacha:libcrypto libssl-chacha:libssl"

source="openssl-${_gitref}.tar.gz::https://github.com/PeterMosmans/openssl/archive/${_gitref}.tar.gz"

_builddir="${srcdir}"/openssl-${_gitref}

build() {
  local _target _optflags
  cd "$_builddir"

  # openssl will prepend crosscompile always core CC et al
  CC=${CC#${CROSS_COMPILE}}
  CXX=${CXX#${CROSS_COMPILE}}
  CPP=${CPP#${CROSS_COMPILE}}

  # determine target OS for openssl
  case "$CARCH" in
  x86)    _target="linux-elf" ;;
  x86_64) _target="linux-x86_64";;
  arm*)   _target="linux-armv4" ;;
  *)  msg "Unable to determine architecture from (CARCH=$CARCH)" ; return 1 ;;
  esac

  # Configure assumes --options are for it, so can't use
  # gcc's --sysroot fake this by overriding CC
  [ -n "$CBUILDROOT" ] && CC="$CC --sysroot=${CBUILDROOT}"

  perl ./Configure $_target \
    --prefix=/usr \
    --libdir=lib \
    --openssldir=/etc/ssl \
    -DOPENSSL_NO_BUF_FREELISTS \
    zlib \
    $CPPFLAGS $CFLAGS $LDFLAGS -Wa,--noexecstack \
    || return 1

  make || return 1
}

package() {
  cd "$_builddir"
  make INSTALL_PREFIX="$pkgdir" MANDIR=/usr/share/man install || return 1

  # c_rehash compat link
  ln -sf openssl "$pkgdir"/usr/bin/c_rehash

  # rename man pages that conflict with man-pages
  local m
  for m in rand.3 err.3 threads.3 passwd.1; do
    mv "$pkgdir"/usr/share/man/man${m/*.}/$m \
       "$pkgdir"/usr/share/man/man${m/*.}/openssl-$m \
      || return 1
  done
}

libcrypto() {
  pkgdesc="Crypto library from openssl-chacha"

  mkdir -p "$subpkgdir"/lib "$subpkgdir"/usr/lib
  for i in "$pkgdir"/usr/lib/libcrypto*; do
    mv $i "$subpkgdir"/lib/
    ln -s ../../lib/${i##*/} "$subpkgdir"/usr/lib/${i##*/}
  done
  mv "$pkgdir"/usr/lib/engines "$subpkgdir"/usr/lib/
}

libssl() {
  pkgdesc="SSL shared libraries from openssl-chacha"

  mkdir -p "$subpkgdir"/lib "$subpkgdir"/usr/lib
  for i in "$pkgdir"/usr/lib/libssl*; do
    mv $i "$subpkgdir"/lib/
    ln -s ../../lib/${i##*/} "$subpkgdir"/usr/lib/${i##*/}
  done
}

md5sums="4b30c3e43c5efde53e5bdfac3900ad10  openssl-f53572f7c8df7e58fbde91caaf38dfe2bb1f3c3a.tar.gz"
sha256sums="324cd0695c84beb5e4d42f42f730b52c3d4c3b66ce5aef396d1f1b5090e73020  openssl-f53572f7c8df7e58fbde91caaf38dfe2bb1f3c3a.tar.gz"
sha512sums="a7c18ff2f48a3e9b9147e5e331702c1006eb1775abc29475ed69e0daab5ac476b1db1d09310e699189b360a9d5c09715d38f62c26cac3126889ba4f2c305468e  openssl-f53572f7c8df7e58fbde91caaf38dfe2bb1f3c3a.tar.gz"
