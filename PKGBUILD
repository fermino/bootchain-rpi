# RFC66 attempt at a bootchain for Raspberry Pi devices

# Maintainer: Fermín Olaiz <fermin@olaiz.net>

pkgname=bootchain-rpi
pkgver=4
pkgrel=1
pkgdesc='kernel-install based boot chain for Raspberry Pi devices'
arch=(any)
url='https://gitlab.archlinux.org/archlinux/rfcs/-/merge_requests/66'
license=('GPL-2.0-only')
provides=(bootchain)
conflicts=(bootchain)
options=('!debug')
depends=(
    bash
    coreutils
    initramfs
    # Re-add when this virtual package is provided
    # kernel
    mkinitcpio
    pacman
    # Eventually kernel-install
    systemd
)

source=(
    # Generic RFC66
    90-kernel-install-add.hook
    60-kernel-install-remove.hook
    85-mkinitcpio-rebuild.hook
    kernel-install-dispatch
    # Raspberry Pi
    10-raspberrypi.conf
    52-rpi-initrd.install
    60-rpi-boot.install
    cmdline.txt
)
sha256sums=('e1e1b4e05feb30e90e1850d7e7dac7c32e96e813a77116aae46bb1e0c221a2ff'
            '7a9bc12d6f63ac5b5d3a5134dd55f4130435b777238f29f2ee359e1414bcf2d8'
            '63c08e61eebab692e51871934fe016c1439d8f58b9602b029b6628a92372d6a5'
            '14897c69b35782834d46d9ae7bff9530a1ef61fb4a478caf0a4b1ea9aa5f6d91'
            '11f40f4a98d4205076c6c5d0d486774c778eb8c73d93447be808980990f501fd'
            'dd8dedd85788ec39ef5c55a0e276fdc8eb5f84f00ed0a094fe40f5c8cc2bd618'
            '79fbee48a926a6ebb8d205d9346b6b6ba0c758dfb40cd17efa97f0205d2741d6'
            '15177c34a76adf8c51ae7a3199ce005288628a04c0306820e146361431b72b21')

package() {
    cd "$srcdir"

    # RFC66 hooks and scripts, hopefully provided by `bootchain` in the future
    LIBALPM_HOOKS=(
        90-kernel-install-add.hook
        60-kernel-install-remove.hook
        # Intended to be kept in sync with mkinitcpio sources (GPLv2)
        # https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio/-/blob/master/libalpm/hooks/90-mkinitcpio-install.hook
        85-mkinitcpio-rebuild.hook
    )
    install -Dm644 -t "$pkgdir/usr/share/libalpm/hooks" "${LIBALPM_HOOKS[@]}"
    install -Dm755 -t "$pkgdir/usr/share/libalpm/scripts" kernel-install-dispatch
    # Mask mkinitcpio hooks that would leave stray initramfs' in /boot
    install -d "$pkgdir/etc/pacman.d/hooks"
    ln -s /dev/null "$pkgdir/etc/pacman.d/hooks/90-mkinitcpio-install.hook"
    ln -s /dev/null "$pkgdir/etc/pacman.d/hooks/60-mkinitcpio-remove.hook"

    # Raspberry Pi specifics
    install -Dm644 -t "$pkgdir/usr/lib/kernel/install.conf.d" 10-raspberrypi.conf
    install -Dm755 -t "$pkgdir/usr/lib/kernel/install.d" \
        60-rpi-boot.install \
        52-rpi-initrd.install

    # Default kernel cmdline (fallback)
    install -Dm644 cmdline.txt "$pkgdir/usr/lib/kernel/cmdline"
}
