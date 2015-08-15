# Maintainer: Charles Bos <charlesbos1 AT gmail>
# Contributor: Balló György <ballogyor+arch at gmail dot com>
# Contributor: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>

pkgname=gnome-settings-daemon-compat
_pkgname=gnome-settings-daemon
pkgver=3.6.4
pkgrel=9
pkgdesc='Compatibility package that provides the background, media keys and mount helpers from GNOME Settings Daemon 3.6'
arch=('i686' 'x86_64')
license=('GPL')
depends=('gnome-settings-daemon')
makedepends=('docbook-xsl' 'intltool' 'xf86-input-wacom' 'ibus')
url="http://www.gnome.org"
source=(http://ftp.gnome.org/pub/gnome/sources/$_pkgname/${pkgver%.*}/$_pkgname-$pkgver.tar.xz
        standalone-background-helper.patch
        standalone-media-keys-helper.patch
        draw-background-unconditionally.patch
        sessionisactive-port.patch
        revert-input-sources.patch
        xinput.patch 
        remove-libgsd-dependency.patch
        move-desktop-file.patch)
sha256sums=('3db993f2dbabc0c9d06a309bb12c9a7104b9cdda414ac4b1c301f5114a441c15'
            '2e7e40175533556493bb58795a2c1b4e53b4baba35d151e632b933c6077456d9'
            'ddc4dfaa8083ff24a7d2fb6138b8c3fee5d4e6c60c24b4e80a39392d0c8f2162'
            '1b6b8216434b766e1389e876cba5d6ab61498c5824f6d2cc5d67dcf58a07842a'
            '0821f469cd168f3a131da513a5f9dd352c06f9bc31d57d79de4dc063fa2de915'
            '02da2467e287620c3b717c7ff5ffea7403cce714d5aa32e27d051b6571451e2a'
            'fe8cafee074e36a7a393c9ae7f65db1c13a0959213aaae94ab8a3543bf20a25d'
            '730f11d5689892fbab9aa2896f3720e813d17e2455f34fd3a0751e49f5b4c26c'
            'c58ace67581a1e14dc21efc8d22dd1f385cd05483d67e8a385b0767a8daf9fb4')

prepare() {
  cd $_pkgname-$pkgver

  # Build background and media keys helpers as a stand alone binary
  patch -Np1 -i ../standalone-background-helper.patch
  patch -Np1 -i ../standalone-media-keys-helper.patch

  # Always draw background
  patch -Np1 -i ../draw-background-unconditionally.patch

  # Port to gnome-session's SessionIsActive property
  patch -Np1 -i ../sessionisactive-port.patch

  # Add compatibility patches for media keys plugin
  patch -Np1 -i ../revert-input-sources.patch
  patch -Np1 -i ../xinput.patch

  # Remove libgsd dependency
  patch -Np1 -i ../remove-libgsd-dependency.patch

  # Move desktop files out of autostart, so they can be easily reusable in custom sessions
  patch -Np1 -i ../move-desktop-file.patch

}

build() {
  cd $_pkgname-$pkgver

  autoreconf -fi
  ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var --libexecdir=/usr/lib/$_pkgname \
              --disable-static --enable-systemd

  # https://bugzilla.gnome.org/show_bug.cgi?id=656231
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool

  make -C plugins/common
  make -C plugins/automount
  make -C plugins/background
  make -C plugins/media-keys
}

package() {
  cd $_pkgname-$pkgver
  make -C plugins/automount DESTDIR="$pkgdir" install
  make -C plugins/background DESTDIR="$pkgdir" install
  make -C plugins/media-keys DESTDIR="$pkgdir" install
}

# vim:set ts=2 sw=2 et:
