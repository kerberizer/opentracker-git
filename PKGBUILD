# Maintainer: Luchesar V. ILIEV <luchesar%2eiliev%40gmail%2ecom>
# Contributor: Dan Johansen <strit83 at gmail dot com>

pkgname=opentracker-git
_pkgname=opentracker
pkgdesc='A open and free bittorrent tracker'

pkgver=2016.10.02
pkgrel=1

arch=('x86_64' 'i686' 'armv7h' 'armv7l' 'aarch64')
url='http://erdgeist.org/arts/software/opentracker/'
license=('custom:Beerware')

provides=("${_pkgname}")
replaces=("${_pkgname}")
conflicts=("${_pkgname}")

makedepends=(
    'cvs'
    'git'
)
optdepends=(
    'perl: sync_daemon.pl support'
)

backup=(
    "etc/${_pkgname}/${_pkgname}.conf"
)

source=(
    "git://erdgeist.org/${_pkgname}"
    "${_pkgname}.service"
    "${_pkgname}-sysusers.conf"
    'LICENSE'
)
sha256sums=(
    'SKIP'
    'ae62c9ddfcb59563891ab4a816d3c09f8690d266cfda76d460605317594e081b'
    '048536abb09e0dafcdef342a4273d5a2deb61b0e82fbd0794c95d7c79fad6ff4'
    '813da9057020fde4a713a6ec8ee11049f91dd91ca0e43c637433c4e758d8efae'
)
install="${_pkgname}.install"

pkgver() {
    cd "${srcdir}/opentracker"
    git log -n 1 --pretty=format:"%cd" --date=short | sed "s/-/./g"
}

prepare() {
    cd "${srcdir}/.."
    if [[ -d libowfat ]]; then
        cd libowfat && cvs -z9 update -d && cd ..
    else
        cvs -d :pserver:cvs@cvs.fefe.de:/cvs -z9 co libowfat
    fi
    rm -rf "${srcdir}/libowfat"
    cp -a libowfat "${srcdir}/"
}

build() {
    cd "${srcdir}/libowfat"
    make

    cd "${srcdir}/${_pkgname}"
    sed \
        -e '/^#FEATURES+=-DWANT_ACCESSLIST_WHITE/s/^#//' \
        -i Makefile
    make
}

package() {
    cd "${srcdir}"

    install -Dm 0644 -t "${pkgdir}/usr/lib/systemd/system" \
        "${srcdir}/${_pkgname}.service"

    install -Dm 0644 "${srcdir}/${_pkgname}-sysusers.conf" \
        "${pkgdir}/usr/lib/sysusers.d/${_pkgname}.conf"

    install -Dm 0755 -t "${pkgdir}/usr/bin" \
        "${srcdir}/${_pkgname}/${_pkgname}" \
        "${srcdir}/${_pkgname}/sync_daemon.pl"

    sed \
        -e "s|\\(access\\.whitelist\\).*|\\1 ./whitelist|" \
        -e "s|\\(access\\.blacklist\\).*|\\1 ./blacklist|" \
        -e "s|\\(tracker\\.rootdir\\).*|\\1 /etc/${_pkgname}|" \
        -e "s|\\(tracker\\.user\\).*|\\1 ${_pkgname}|" \
        -i "${srcdir}/${_pkgname}/${_pkgname}.conf.sample"

    install -Dm 0644 "${srcdir}/${_pkgname}/${_pkgname}.conf.sample" \
        "${pkgdir}/etc/${_pkgname}/${_pkgname}.conf"

    install -Dm 0644 -t "${pkgdir}/usr/share/doc/${_pkgname}" "${srcdir}/${_pkgname}"/README*
    install -Dm 0644 -t "${pkgdir}/usr/share/licenses/${_pkgname}" "${srcdir}/LICENSE"
}

# vim:set ts=4 sts=4 sw=4 et:
