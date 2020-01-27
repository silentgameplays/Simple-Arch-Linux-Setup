# ArchLinux-Installation-From-Scratch
ArchLinux Installation From Scratch UEFI,GUI,Steam,VLC
# 0. Downloading the installation image and creating a bootable device using rufus:

* Download image here via torrent or direct link:https://www.archlinux.org/download/
* Use Rufus and select GPT and ISO mode for the image

# 1.Creating partitions using cfdisk:

# Run this command and delete everything,from free space create the following:

* cfdisk 
* /dev/sdx1 512M  Fat32(EFI)

# NB!The size of the EFI must be 512MB and type should be EFI

# Swap patition size can be around 3-10GB 

* /dev/sdx2 8-10GB swap

# Main partition,remaining size can be as large as you want it to be: 

* /dev/sdx3 200GB ext 4

# 2.Formatting the drives:

# For the /dev/sdx1 use the following command:

* mkfs.fat -F32 /dev/sdx1

# For the swap /dev/sdx2 use the following commands:

*  mkswap /dev/sdX2
*  swapon /dev/sdX2

# For the primary partition use the following command:

* mkfs.ext4 /dev/sdX3

# 3.Mounting the drives:

* mount /dev/sdX3 /mnt

# 4 Checking if everything worked:

* lsblk

# 5.Installing basic packages and stuff:

* pacstrap /mnt base base-devel linux linux-firmware nano vim dhcpcd 

# 6. Generating fstab file:

* genfstab -U /mnt >> /mnt/etc/fstab
# 7.Switching to root.All the below commands must be used as root!

* arch-chroot /mnt /bin/bash

# 8.Checking network connection:

* ip link

# 9.Setting date,region and time,use these commands:

* timedatectl set-ntp true
* timedatectl list-timezones
* timedatectl set-timezone Zone/SubZone
* ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
* hwclock --systohc

# 10.Setting the hostname

* hostnamectl set-hostname myhostname

# 11.Setting locales:

* localectl set-locale LANG=en_US.UTF-8
* locale-gen

# 12.Adding a main user:

* useradd -g users -G power,storage,wheel -m test

# 13.Assigning passwords for root and main user

# Password for root:

* passwd 

# Password for main user:

* passwd your_new_user

# 14.Downloading the text editors(in case you did'not add them already):

* pacman -S nano
* pacman -S vim

# 15.Installing sudo (in case it's not installed already):

* pacman -S sudo

# 16.Editing the sudoers file:

* visudo

# Uncomment the needed settings in sudoers file and add the main user into it:

# Lines safe to uncomment :ALL= ALL(ALL) ALL

* :w! + Enter to exit and write changes
* :q + Enter exit

# 17.Grub and efi tools installation(very important step!):

* pacman -S grub efibootmgr dosfstools os-prober mtools fuse2 

# 18. Creating efi boot directory on the EFI partition:

* mkdir /boot/efi

# 19. Mounting the FAT32 EFI partition:

* mount /dev/sdx1 /boot/efi  #Mount FAT32 EFI partition 

# 20. Grub installation and configuration(very important,otherwise system will not boot!): 

* grub-install --target=x86_64-efi   --bootloader-id=grub --efi-directory=/boot/efi 
* grub-mkconfig -o /boot/grub/grub.cfg

# 21.Additional security measures so that the boot does not become unbootable:

* mkdir /boot/efi/EFI/BOOT
* cp /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/BOOT/BOOTX64.EFI

# 22.Creating and editing the efi boot file so that system becomes bootable:

* nano /boot/efi/startup.nsh
* bcf boot add 1 fs0:\EFI\GRUB\grubx64.efi “My grub bootloader”

# 23.Exiting root and rebboting:

* exit

# 24.Unmounting the live partitions and rebooting:

* umount -R /mnt
* reboot

# 25.First steps after reboot under root or using sudo:
* sudo systemctl enable dhcpcd.service
* sudo pacman -S dhcpcd 
* sudo nano /etc/systemd/network/enp0s3.network
or
* sudo vi /etc/systemd/network/enp0s3.network
# Be sure that the below lines are entered into the file:

* [Match]
* name=en*
* [Network]
* DHCP=yes

# 26.Run these commands before the next reboot:

* sudo systemctl restart systemd-networkd
* sudo systemctl enable systemd-networkd

# 27.Reboot again:

* sudo reboot

# 28. Install intel firmware it is important:

* sudo pacman -Syu intel-ucode

# 29. Install networkmanager and dhcpcd if it is not included:

* pacman -Syu networkmanager
* pacman -S dhcpcd 

# 30. Install Xorg: 

* sudo pacman -Syu xorg 

# 31. Install alsa audio drivers and utilities:

* sudo pacman -Syu alsa
* sudo pacman -Syu alsa-utils

# 32. Check if alsamixer is working:

* alsamixer

# 33. Install nvidia drivers and utilities:

* sudo pacman -Syu nvidia-lts
* sudo pacman -Syu nvidia-utils 

# 34. Install the terminal:

* sudo pacman -Syu lxterminal

# 35. Install the most optimal,lightweight and compatible with NVIDIA drivers GUI Deepin and extras: 

* sudo pacman -S deepin
* sudo pacman -S deepin-extras

# 36. Enable the GUI to start at launch: 

* systemctl enable lightdm.service
* systemctl start lightdm.service

# 37. Once everythig is working we can enable multilib(same as multiarch) for Steam: 

* sudo pacman -Suy
# Edit the pacman mirror list:

* sudo nano /etc/pacman.d/mirrorlist

# Uncomment these lines:

* [multilib]
* Include = /etc/pacman.d/mirrorlist

# 38. Update pacman libraries:

* sudo pacman -Sy

# 39. Install additional nvidia-utils,required for steam: 
* sudo pacman -S lib32-nvidia-utils

# 40. Install Steam 
* sudo pacman -Suy steam

# 41. Install VLC

* sudo pacman -Syu vlc


That's it you are good for using pure ArchLinux with Deppin as GUI(others don't work too well with NVIDIA drivers,but give xfce4,mate,LXDE a shot as well)

Thank you!

#gimalaji_blake
