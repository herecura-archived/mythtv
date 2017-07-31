# $Id$
# Maintainer: Jonathan Conder <jonno.conder@gmail.com>
# Contributor: Giovanni Scafora <giovanni@archlinux.org>
# Contributor: Juergen Hoetzel <juergen@archlinux.org>
# Contributor: <kleptophobiac@gmail.com>
# Contributor: dorphell <dorphell@archlinux.org>

pkgname=mythtv
pkgver=29.0
pkgrel=1
epoch=1
pkgdesc="A Homebrew PVR project"
arch=('i686' 'x86_64')
url="http://www.mythtv.org/"
license=('GPL')
depends=('avahi' 'fftw' 'lame' 'libass' 'libavc1394' 'libcdio' 'libiec61883'
         'libgl' 'libpulse' 'libva' 'libvpx' 'libxinerama' 'lirc' 'mariadb-clients'
         'mysql-python' 'perl-dbd-mysql' 'perl-io-socket-inet6' 'perl-libwww'
         'perl-net-upnp' 'python2-lxml' 'qt5-webkit' 'qt5-script' 'taglib' 'urlgrabber'
         'libx264' 'libvdpau' 'exiv2' 'libxrandr' 'jack')
makedepends=('glew' 'libcec' 'libxml2' 'mesa' 'mesa-libgl' 'openssl' 'yasm' 'x264' 'gdb')
optdepends=('glew: for GPU commercial flagging'
            'libcec: for consumer electronics control capabilities'
            'libxml2: to read blu-ray metadata'
            'openssl: for AirTunes (RAOP) support')
conflicts=('myththemes' 'mythplugins-mythvideo')
replaces=('myththemes' 'mythplugins-mythvideo')
install='mythtv.install'
source=("$pkgname-$pkgver.tar.gz::https://github.com/MythTV/$pkgname/archive/v$pkgver.tar.gz"
        'mythbackend.service' '99-mythbackend.rules')
sha256sums=('3c24acf3aab3a52e66222331b3eb104f5136f3b045fef8e0ba5623bda581b2a9'
            'ed5ca54de26b7cd8a64e09626eed6e09f35d677daf88c530bb24cc4252bcce6d'
            'ecfd02bbbef5de9773f4de2c52e9b2b382ce8137735f249d7900270d304fd333')

prepare() {
  cd $pkgname-$pkgver/$pkgname

  find 'bindings/python' 'contrib' 'programs/scripts' -type f | xargs sed -i 's@^#!.*python$@#!/usr/bin/python2@'
}

build() {
  cd $pkgname-$pkgver/$pkgname

  ARCH="${CARCH/_/-}"
  ./configure --prefix=/usr \
              --cpu="$ARCH" \
              --disable-altivec \
              --disable-audio-jack \
              --disable-ccache \
              --disable-distcc \
              --enable-libfftw3 \
              --enable-libmp3lame \
              --enable-libvpx \
              --enable-libx264 \
              --enable-vaapi \
              --python=python2 \
              --perl-config-opts=INSTALLDIRS=vendor
  make
}

package() {
  cd $pkgname-$pkgver/$pkgname
  make INSTALL_ROOT="$pkgdir" install

  install -D -m644 "$srcdir/mythbackend.service" "$pkgdir/usr/lib/systemd/system/mythbackend.service"
  install -D -m644 'database/mc.sql' "$pkgdir/usr/share/mythtv/mc.sql"

  mkdir -p "$pkgdir/usr/share/mythtv"
  cp -R 'contrib' "$pkgdir/usr/share/mythtv"
  mkdir -p "$pkgdir/var/log/mythtv"

# Install udev rules https://www.mythtv.org/wiki/Systemd_mythbackend_Configuration#Delay_starting_the_backend_until_tuners_have_initialized
  install -Dm644 "$srcdir"/99-mythbackend.rules "$pkgdir"/usr/lib/udev/rules.d/99-mythbackend.rules
}
