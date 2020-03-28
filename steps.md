Following guide on: https://wiki.archlinux.org/index.php/installation_guide#Initramfs
Some things to keep in mind first as of time of installation on 3/28/2020
* As of systemd version 245, needs to be downgraded to 244 as it will hang on login
* Not dualbooted
* SSD has been switched out from the stock Toshiba to a Samsung EVO pro 1TB
* Because of this, the Boot/EFI partition will need to be made bootable
* I'm still a beginner when it comes to this, so some instructions may sound strange. Comments and criticisms are welcomed!
* This is also assuming you're installing via USB and made the USB bootable, and have edited the BIOS settings to boot from USB

### Connect to internet

`wifi-menu`

`ping archlinux.org`

### Update system clock

`timedatectl set-ntp true`

`timedatectl status`

**Check for disk partitions, partition disks**

### Checking for existing partitions:

`lsblk`

#### Partitioning the drive

`cgdisk /dev/nvme0n1`

**NOTE**: There's different options to partition your disk, you can use `gdisk` for a text/terminal based application, or you can use `cgdisk` for a curses-type interface. Personal preference, those two tools are both GPT fdisk.

**Boot Partition**

First sector: default

Second sector: 512M

Hex code: ef00 (for EFI system partition or ESP)

Partition name: Whatever you want lol

**Swap Partition**

First sector: default

Size in sectors: +22G

Hex code: 8300 (Linux Filesystem)

Partition name: whatever you want

