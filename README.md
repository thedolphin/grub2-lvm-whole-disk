# grub2-lvm-whole-disk

A patch enabling GRUB 2 to install directly onto a disk using LVM, which is otherwise unsupported by default.

This patch is especially useful for BIOS-based systems when:

- Working with disks larger than 2 TB (common in older setups with large RAID arrays).
- Using dynamic disks for guest systems in modern virtualization environments.

Original patch by Gabriel de Perthuis <g2p.code@gmail.com>  
See discussion at: http://lists.gnu.org/archive/html/grub-devel/2013-09/msg00113.html

## Background

GRUB 2 supports booting from LVM volumes, but—surprisingly—does not support installing the bootloader directly onto an LVM-managed disk. This repository provides a patch that adds that capability, allowing you to install GRUB directly onto a disk with LVM.

## Preparation

Before installing, the LVM disk must be initialized using:

```bash
pvcreate --bootloaderareasize 1m /dev/your-lvm-disk
```

This ensures a dedicated 1 MB "bootloader area" that GRUB can use.

## Installation Instructions

* Prepare your LVM disk (see “Preparation” above).
* Download the appropriate OS-specific binary from the grub-install/ directory in this repository.
* Use the downloaded binary to install GRUB as usual. For example:

```bash
    ./grub-install-<os-specific-binary> /dev/your-lvm-disk
```

This patch allows the installer to write the bootloader directly onto an LVM disk—something not possible with standard GRUB.

## Repository Contents

lvm-support-*.patch – Patches tailored for various GRUB2 versions.

grub-install/ – Prebuilt binaries for installing GRUB with LVM support.

(Optional) Dockerfiles for building on Debian 13 (Dockerfile.debian13).
