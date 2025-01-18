# Maintainer: Julian Droske <juliandroske@outlook.com>

_pkgdesc='Extensible, customizable, self-documenting free/libre text editor'
_runtime_common_deps=(
  musl
  acl
  dbus
  ncurses
  libxml2
  zlib-ng
)
_runtime_gui_deps=(
  # dbus
  at-spi2-core # provides libatk-1.0.so

  # codec
  libjpeg
  libpng
  librsvg
  dav1d
  libtiff

  # font / graphics
  fontconfig
  freetype2
  harfbuzz
  pango
  cairo
  glib
  gtk3
  gdk-pixbuf

  # sound
  alsa-lib
)
pkgbase=emacs
pkgname=(
  emacs
  emacs-nogui
)
pkgver=29.4
pkgrel=1
url='https://www.gnu.org/software/emacs/emacs.html'
pkgdesc="${_pkgdesc}"
arch=(x86_64 aarch64 riscv64 loongarch64)
license=('GPL-3.0-or-later AND LGPL-2.1-or-later')
provides=(emacs)
conflicts=(emacs)
depends=(
  "${_runtime_common_deps[@]}"
  "${_runtime_gui_deps[@]}"
)
makedepends=(
  make
  texinfo

  # sound for gui
  linux-headers
)
source=(
  "https://ftp.gnu.org/gnu/emacs/emacs-${pkgver}.tar.gz"
  include-paths-header-musl.patch
)
sha256sums=(
  '1adb1b9a2c6cdb316609b3e86b0ba1ceb523f8de540cfdda2aec95b6a5343abf'
  '6712f6f6be590670c7c03c9f07bca5c55768b6b87503b39af38b1f3a4cfa3332'
)

prepare() {
  _patch_ "emacs-${pkgver}"

  # cp in busybox has no --reflink option to perform CoW
  cp -ar "emacs-${pkgver}" "emacs-nogui-${pkgver}"
}

build() {
  local _confflags="
    --sysconfdir=/etc
    --prefix=/usr
    --libexecdir=/usr/lib
    --localstatedir=/var
    --without-gnutls
    --with-harfbuzz
    --disable-build-details
  "

  cd "emacs-${pkgver}"
  ./configure $_confflags \
    --with-pgtk
  make

  cd "../emacs-nogui-${pkgver}"
  ./configure $_confflags \
    --without-x \
    --without-sound
  make
}

# Remove systemd-related directories hard-coded in Makefile
_postpackage() {
  rm -rf "${pkgdir}/usr/lib/systemd"
}

package_emacs() {
  pkgdesc="${_pkgdesc}"
  depends=(
    "${_runtime_common_deps[@]}"
    "${_runtime_gui_deps[@]}"
  )

  cd "${pkgname}-${pkgver}"
  make DESTDIR="${pkgdir}" install

  _postpackage
}

package_emacs-nogui() {
  pkgdesc="${_pkgdesc} - without GUI"
  depends=(
    "${_runtime_common_deps[@]}"
  )
  provides+=(emacs-nox)

  cd "${pkgname}-${pkgver}"
  make DESTDIR="${pkgdir}" install

  _postpackage
}
