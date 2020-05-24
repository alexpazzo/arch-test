# Installation arch

## Change keyboard 🎹
```console
root@archiso# loadkeys it
```

## Partition EFI
```console
root@archiso# fdisk -l # lsblk less info
root@archiso# fdisk /dev/sdx # or nvme...
root@archiso# #command for fdisk
-> d # delete partition, delete all
-> g # create a gpt partition table
-> n # new partition (+500M for EFI, +100G for /, the rest for /home)
-> t # select type of partition, in this case (1) for EFI for parition number 1
-> t # select type of partition, in this case (1) for root linux x86_64 for parition number 2
-> w # wqrite the configuration
#format the partitions
root@archiso# mkfs.fat -F32 /dev/sdx # get the correct partition (the 500M one)
root@archiso# mkfs.ext4 /dev/sdx # fomrat the other 2 in ext4
```ip 

## Mount partition 
```console
root@archiso# mount /dev/sdax /mnt # the root (/) 100G one
root@archiso# mkdir -p /mnt/boot/efi
root@archiso# mount /dev/sdax /mnt/boot/efi
root@archiso# mkdir /mnt/home
root@archiso# mount /dev/sdax /mnt/home # the home partition (/home)
root@archiso# lsblk # check if all ok!
```

## Connect to internet (wifi) 📡
```console
root@archiso# wifi-menu
root@archiso# ip link
```

## Set date 
```console
root@archiso# timedatectl set-ntp true
```

## Update Mirriorlist OPTIONAL
```console
root@archiso# pacman -Syy reflector
root@archiso# reflector --verbose -l 200 -p http --sort rate --save /etc/pacman.d/mirrorlist
```

## Base Install 🎅
```console
root@archiso# pacstrap /mnt base base-devel linux linux-firmware vim networkmanager # install the base package for arch in the /mnt  
root@archiso# genfstab -U /mnt >> /mnt/etc/fstab 
root@archiso# arch-chroot /mnt # change root in the new file system
```

# Set timezone
```console
root@archiso# ln -sf /usr/share/zoneinfo/{Region}/{City} /etc/localtime # Change Region and City
root@archiso# hwclock --systohc # generate adjtime
```


# Set locale
```console
root@archiso# vim /etc/locale.conf # -> LANG=en_US.UTF-8
root@archiso# locale-gen
root@archiso# vim /etc/vconsole.conf # -> KEYMAP=it non se funziona
```

# Network configuration
```console
root@archiso# vim /etc/hostname # -> myhostname
root@archiso# vim /etc/hosts # -> 127.0.0.1	localhost ::1		localhost 127.0.1.1	myhostname.localdomain	myhostname
root@archiso# systemctl enable NetworkManager
```

# root password
```console
root@archiso# passwd
```

# add user
```console
root@archiso# useradd -m {nameuser} # add user
root@archiso# passwd {nameuser} # change password for it
root@archiso#  pacman -S sudo # install sudo
root@archiso# usermod -aG wheel,audio,video,optical,storage {nameuser} # add basic group to user
root@archiso# groups {nameuser} # check the groups
root@archiso# visudo # uncomment # %wheel
```

# install bootloader (grub)
```console
root@archiso# pacman -S grub efibootmgr
root@archiso# grub-install /dev/sdx
root@archiso# grub-mkconfig -o /boot/grub/grub.cfg
```
