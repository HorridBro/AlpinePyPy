# Contributor: Mihnea Saracin <mihnea.saracin@rinftech.com>
# Maintainer: Mihnea Saracin <mihnea.saracin@rinftech.com>
pkgname=pypy
pkgver=5.7.1
pkgrel=0
pkgdesc="A fast, compliant alternative implementation of the Python 2 language"
url="https://bitbucket.org/pypy"
arch="all"
license="MIT"
depends="libssl1.0 libcrypto1.0 libffi libbz2 "
makedepends="python linux-headers libffi-dev pkgconf zlib-dev bzip2-dev sqlite-dev ncurses-dev readline-dev libressl-dev expat-dev gdbm-dev tk-dev py2-cffi paxmark"
subpackages="$pkgname-dev $pkgname-doc"
source="https://bitbucket.org/$pkgname/$pkgname/downloads/pypy2-v$pkgver-src.tar.bz2
        alpine.patch"
builddir="$srcdir/pypy2-v$pkgver-src/pypy/goal"


build() {
        cd "$builddir"
        python22 ../../rpython/bin/rpython --opt=jit --nopax targetpypystandalone.py || return 1
}

package() {
        mkdir -p "$pkgdir"
        cd "$builddir/../tool/release"
        ./package.py --archive-name pypy2-v$pkgver --builddir "$srcdir/pypy2-v$pkgver-alpine" || return 1
}



sha512sums="1ad2dddb40c28d2d3e95a9f0730e765d981dee6e2d0664cf1274eb7c1021690a848c3485c846eac8a8b64425b44946b5b2d223058ec4699155a2122ee7d38b75  pypy2-v5.7.1-src.tar.bz2
3df182c402ee6f37f81a87679ed7e92f7375c70beacc960548608dbff0f627037d7995c54e8e3cad544f9e0b6b08d00d8419d57b4dbfe5290bd34cbcdbb76775  alpine.patch"

