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

### Partitioning the drive

`cgdisk /dev/nvme0n1`

**NOTE**: There's different options to partition your disk, you can use `gdisk` for a text/terminal based application, or you can use `cgdisk` for a curses-type interface. Personal preference, those two tools are both GPT fdisk.

**Boot Partition**

First sector: default

Second sector: 512M

Hex code: ef00 (for EFI system partition or ESP)

Partition name: Whatever you want lol

**Swap Partition**

First sector: default

Size in sectors: 22G

Hex code: 8300 (Linux Filesystem)

Partition name: whatever you want

**Root Partition**

First sector: default

Size in sectors: 30G

Hex code: 8300 (Linux Filesystem)

Partition name: whatever

**Home partition**

First sector: default

Size in sectors: Rest of hard drive (just hit enter when asked to allocate space)

Hex code: 8300

Partition name: whatever

**Write partition table to disk**

**Set up swap partition**

`mkswap /dev/nvme0n1p2`

`swapon /dev/nvme0n1p2`

Running `lsblk` should now show [SWAP] next to /dev/nvme0n1p2

### Mount partitions

`mount /dev/nvme0n1p3 /mnt`

**Create home and boot directory for mounting**

`mkdir /mnt/boot`

`mkdir /mnt/home`

**Mount boot and home**

`mount /dev/nvme0n1p1 /mnt/boot`

`mount/dev/nvme0n1p4 /mnt/home`

### Installing the base system

`pacstrap /mnt base base-devel linux linux-firmware vim git efibootmgr binutils iw sudo zsh dialog make netctl`

* Most of these packages are optional, you can just install base, base-devel, linux, and linux-firmware if you want but it's recommended to have a text editor, networking software, and other development software that you may need like make, etc.

### Generate the fstab

`genfstab -U /mnt >> /mnt/etc/fstab`

You can use `-U` or `-L` to define by either UUID, or Labels. Open the fstab file with editor of choice to check for errors.

### Change root into the installed system

`arch-chroot /mnt`

### DOWNGRADE SYSTEMD TO 244

The most recent version of systemd at the time of writing this (version 245) has some issues with not being able to login.

Documented here: https://bugs.archlinux.org/task/65825, 

Downgrading to systemd version 244 seems to resolve it. To do this, systemd can be downloaded and installed from the archive.

Reference: https://wiki.archlinux.org/index.php/Pacman#Additional_commands

`pacman -U http://archive.archlinux.org/packages/s/systemd/systemd-244.1-1-x86_64.pkg.tar.xz`

### Set time zone

`ln -sf /usr/share/zoneinfo/Pacific/Honolulu /etc/localtime`

### Generate /etc/adjtime

`hwclock --systohc`

### Generate locale

`vim /etc/locale.gen`

Search for `en_US.UTF-8 UTF-8` and uncomment it. Then, generate with:

`locale-gen`

Create/edit locale.conf file and set LANG variable:

`vim /etc/locale.conf`

In locale.conf:

`LANG=en_US.UTF-8`

### Network configuration

Create hostname file

`vim /etc/hostname`

Enter hostname and exit

Add entries to hosts file: /etc/hosts

`127.0.0.1       localhost
 ::1.            localhost
 127.0.1.1.      arch-xps.localdomain arch-xps`
 




