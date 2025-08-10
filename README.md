# grub2-lvm-whole-disk

A patch enabling GRUB 2 to install directly onto a disk using LVM, which is otherwise unsupported by default.

This patch is especially useful for BIOS-based systems when:

- Working with disks larger than 2 TB (common in older setups with large RAID arrays).
- Using dynamic disks for guest systems in modern virtualization environments.

Original patch by Gabriel de Perthuis <g2p.code@gmail.com>  
See discussion at: http://lists.gnu.org/archive/html/grub-devel/2013-09/msg00113.html

## Background

GRUB 2 supports booting from LVM volumes, but — surprisingly — does not support installing the bootloader directly onto an LVM-managed disk. This repository provides a patch that adds that capability, allowing you to install GRUB directly onto a disk with LVM.

## Serving suggestion — product may not look exactly as shown

1. Prepare the VM by adding a second disk for `/boot`. Alternatively, prepare a bare server and any kind of disk for temporarily placing `/boot`, for example, a USB flash drive.
2. Boot from any live distro media, and prepare the primary disk for the system: `pvcreate --bootloaderareaseize 1m /dev/<disk>`. Rescue shells provided by installation media can be suitable for this purpose, but Debian needs some more sophisticated moves. Then create the volume group and logical volumes as you like. Personally, I settled on a minimal layout with separate `/` and `/var`.
3. Boot the installer. Pick the prepared volumes for the system. Oddly enough, neither Debbie’s installer nor the Red Cap’s Anaconda will let you create an LVM across the entire disk, but both are perfectly fine with you selecting volumes that already exist. Place `/boot` on the second disk, and also select the second disk for installing the bootloader. Remember to switch the boot drive in BIOS or VM configuration.
4. After booting into the installed OS, remount `/boot` to `/mnt` and move everything into the root partition, then remove `/boot` from `/etc/fstab`. If you don't remove `/boot` from `fstab`, the system won’t boot.
5. Download the appropriate binary from the repo and run it like a normal `grub-install`, using the LVM disk as the destination. Once the operating system is installed, I have never seen any updates overwrite the bootloader again.
6. On Debian, run `update-grub`. On RHEL, more steps are needed — fix files in `/boot/loader` by adding `/boot/` to all paths, then run: `grub2-mkconfig -o /etc/grub.cfg`
7. Reboot, switch back the boot drive in BIOS or VM configuration and voilà.
8. Remove the second drive as it is no longer needed.

Once installed 
