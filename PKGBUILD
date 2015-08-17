# quagga routing suite PKGBUILD
# Forked from quagga by helios <helios+aur@wiresphere.de>
#
# Thanks to quagga AUR Maintainer:
# Contributor: shild <shildv@gmail.com> 
# Contributor: Georg Grabler (STiAT) <ggrabler@gmail.com>
# Contributor: Bart Kos, bro at fast-stable-secure dot net
# Contributor: HomeCreate <homecreate@list.ru>
#
# And equinox <dl@diac24.net>

pkgname=quagga-dn42
_pkgname=quagga
pkgver=20111222
pkgrel=1
pkgdesc="BGP/OSPF/ISIS/RIP/RIPNG routing daemon suite. With AS4, advanced dumpfile output and IPv6 linklocal peering support"
arch=('i686' 'x86_64')
url="http://www.diac24.net/files/quagga/info-1.1.0-dn42.11.html"
license=('GPL2')
makedepends=('git')
depends=('libcap' 'libnl' 'net-snmp' 'readline' 'ncurses' 'iproute')
backup=()
install=quagga.install
source=('quagga.rc'
        'quagga.conf')
md5sums=('be82af403747a29911206fb3e5fa7644'
         '0c4f2dd13c7dac1b73de923d1d5e4e17')
provides=('quagga')
conflicts=('quagga' 'quagga-patched')

_gitroot='git://git.spaceboyz.net/equinox/quagga.git'
_gitname='quagga'

build() {
    cd $srcdir

    if [ -e ${_gitname} ] ; then
      cd ${_gitname}
      git pull
      cd ..
    else
      git clone ${_gitroot} ${_gitname}
    fi

    cd $srcdir/${_gitname}

    ./bootstrap.sh
    ./configure --prefix=/usr --sysconfdir=/etc/quagga --localstatedir=/run/quagga \
                --enable-exampledir=/usr/share/doc/quagga/examples \
                --enable-vtysh \
                --enable-isisd \
                --enable-isis-topology \
                --enable-netlink \
                --enable-snmp \
                --enable-irdp \
                --enable-pcreposix \
                --enable-multipath=64 \
                --enable-user=quagga \
                --enable-group=quagga \
                --enable-configfile-mask=0640 \
                --enable-logfile-mask=0640 \
                --enable-ospf-te \
                --enable-opaque-lsa \
                --disable-tcp-zebra \

    make
}

package() {
  # initscripts files
  for d in zebra ripd ripngd bgpd ospfd ospf6d isisd; do
    install -D -m 755 ${_pkgname}.rc "$pkgdir/etc/rc.d/$d"
    install -D -m 644 ${_pkgname}.conf "$pkgdir/etc/conf.d/$d"
    backup+=("etc/conf.d/$d")
  done

  # create /run/quagga directory at startup
  install -D -m 644 /dev/null "$pkgdir/usr/lib/tmpfiles.d/${_pkgname}.conf"
  echo "d /run/${_pkgname} 0750 ${_pkgname} ${_pkgname}" > "$pkgdir/usr/lib/tmpfiles.d/${_pkgname}.conf"

  # soft
  cd ${_gitname}
  make DESTDIR=$pkgdir install
}
