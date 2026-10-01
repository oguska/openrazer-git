# Maintainer: Luca Weiss <luca (at) z3ntu (dot) xyz>
# Contributor: Gabriele Musco <emaildigabry@gmail.com>

pkgname=('openrazer-daemon-git' 'openrazer-driver-dkms-git' 'openrazer-meta-git' 'python-openrazer-git')
pkgbase=openrazer-git
_pkgbase=openrazer
pkgver=3.12.1.r41.g6820f9da
pkgrel=1
pkgdesc='Community-led effort to support Razer peripherals on Linux (git version)'
arch=('any')
url=https://openrazer.github.io
license=('GPL-2.0-or-later')
makedepends=('git' 'python-setuptools')
source=("git+https://github.com/openrazer/openrazer.git"
        "huntsman-v3-x-tkl.patch::https://github.com/alex-PT78/openrazer/commit/e6ba767751191c05129fe89971ca3f4ed6b82d53.patch"
        "huntsman-v3-x-tkl-daemon.patch::https://github.com/alex-PT78/openrazer/commit/21ca5f9cf3056fb8a323cf1b10b8f0e65af685b3.patch"
        "huntsman-v3-x-tkl-cleanup.patch::https://github.com/alex-PT78/openrazer/commit/d2c073a168741054cb09f6c8231b3d851ae0180b.patch"
        'clang-kernel-build.patch')
sha256sums=('SKIP'
            '52760b8ae1bd355ca7cb5db115ea84d24d6a73ac5209dff6827b74e008ad5f7a'
            '513f60790a6bf13a8a44c878d3125947f6e78e2742c5b011eef6721f2839b106'
            'fb83b93e282ecf56e1da78bcce5b48f50ce32bf7533cb83e2ca77db51e11398b'
            'c6ec078e425c1efbf911f4538545cb4ad6dde0a10ae581c7fd8db68a991e5228')

pkgver() {
  cd "$_pkgbase"
  # Try git describe, fallback to revision count and commit hash if no tags exist
  if git describe --long > /dev/null 2>&1; then
    git describe --long | sed 's/^v//;s/\([^-]*-g\)/r\1/;s/-/./g'
  else
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
  fi
}

prepare() {
  cd "$_pkgbase"

  patch -Np1 -i "$srcdir/huntsman-v3-x-tkl.patch"
  patch -Np1 -i "$srcdir/huntsman-v3-x-tkl-daemon.patch"
  patch -Np1 -i "$srcdir/huntsman-v3-x-tkl-cleanup.patch"
  patch -Np1 -i "$srcdir/clang-kernel-build.patch"
  # Do a sanity check in the environment of the builder so the build process doesn't place files into a wrong directory.
  # If you think this is incorrect you can always remove this from the PKGBUILD, but then please don't complain if it doesn't work.
  if [ "$(which python3)" != "/usr/bin/python3" ]; then
    echo "ERROR: Your 'python3' does not point to /usr/bin/python3 but to $(which python3), likely a custom environment like anaconda."
    echo "Please build this package in a clean chroot (e.g. with https://wiki.archlinux.org/title/DeveloperWiki:Building_in_a_clean_chroot) or point your PATH variable to prefer /usr/bin/ temporarily."
    return 1
  fi
}

package_openrazer-daemon-git() {
  pkgdesc='Userspace daemon that abstracts access to the kernel driver. Provides a DBus service for applications to use'
  depends=(
    'libnotify'
    'openrazer-driver-dkms'
    'python-daemonize'
    'python-dbus'
    'python-gobject'
    'python-pyudev'
    'python-setproctitle'
    'xautomation'
  )
  provides=('openrazer-daemon')
  conflicts=('openrazer-daemon')
  install=openrazer-daemon-git.install

  cd "$_pkgbase"
  make DESTDIR="$pkgdir" daemon_install
}

package_openrazer-driver-dkms-git() {
  pkgdesc='OpenRazer kernel modules sources'
  depends=('dkms')
  provides=('openrazer-driver-dkms')
  conflicts=('openrazer-driver-dkms')
  install=openrazer-driver-dkms-git.install

  cd $_pkgbase
  make DESTDIR="$pkgdir" setup_dkms udev_install
}

package_openrazer-meta-git() {
  pkgdesc="Meta package for installing all required openrazer packages."
  depends=('openrazer-driver-dkms' 'openrazer-daemon' 'python-openrazer')
  optdepends=('polychromatic: frontend'
              'razergenie: qt frontend'
              'razercommander: gtk frontend')
  provides=('openrazer-meta')
  conflicts=('openrazer-meta')
}

package_python-openrazer-git() {
  pkgdesc='Library for interacting with the OpenRazer daemon'
  depends=('openrazer-daemon' 'python-numpy')
  provides=('python-openrazer')
  conflicts=('python-openrazer')

  cd $_pkgbase
  make DESTDIR="$pkgdir" python_library_install
}
