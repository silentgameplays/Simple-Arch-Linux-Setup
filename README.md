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

# 26. Install networkmanager and dhcpcd if it is not included:

* sudo pacman -S networkmanager

# 27.Run these commands before the next reboot:

* sudo systemctl restart systemd-networkd
* sudo systemctl enable systemd-networkd

# 28.Reboot again:

* sudo reboot

# 29. Install intel firmware it is important:

* sudo pacman -Syu intel-ucode


# 30. Install Xorg: 

* sudo pacman -S xorg xorg-server xorg-xinit xterm xf86-video-vesa mesa xorg-xrandr

# 31. Install alsa audio drivers and utilities:

* sudo pacman -S alsa
* sudo pacman -S alsa-utils

# 32. Check if alsamixer is working:

* alsamixer

# 33. Install the terminal:

* sudo pacman -S lxterminal

# 34. Install Deepin/Gnome/XFCE/KDE/Cinnamon/LXDE/LXQt desktop environments(choose one): 

# Xfce

* pacman -S xfsce4 xfce4-goodies

# Deepin:

* sudo mkdir home
* sudo pacman -S deepin
* sudo pacman -S deepin-extra

# Gnome

* sudo pacman -S gnome gnome-extra

# KDE

* sudo pacman -S plasma kde-applications

# Cinnamon:

* sudo pacman -S cinnamon

# MATE

* sudo pacman -S mate mate-extra

# LXDE

* sudo pacman -S lxde

# LXQt

* sudo pacman -S lxqt

# NB!:Removing anything can be done by:

* sudo pacman -Rscn application

# 35. Enable the GUI desktop to start at launch: 

# In case lightdm is missing: 
* pacman -S lightdm
* sudo systemctl enable lightdm.service
* sudo systemctl start lightdm.service

# For GNOME its better to use the preinstalled:
# In case gdm is missing
* pacman -S gdm
* systemctl enable gdm.service
* systemctl start  gdm.service

# For KDE use:

* pacman -S sddm
* systemctl enable sddm
* systemctl start sddm

# 36. Edit the pacman conf file to enable mirror list so we can enable multilib(same as multiarch) for Steam and proprietary drivers: 

* sudo nano /etc/pacman.conf

# Uncomment these lines:

* [multilib]
* Include = /etc/pacman.d/mirrorlist

# 37. Update pacman libraries and system:

* sudo pacman -Syu

# 38. Install nvidia drivers and utilities last!:

* sudo pacman -S nvidia
* sudo pacman -S nvidia-utils
* sudo pacman -S lib32-nvidia-utils
* sudo reboot

# 39. Install Steam 
* sudo pacman -S steam

# 40. Install VLC and other stuff

* sudo pacman -S vlc
* sudo pacman -S libreoffice-fresh
* sudo pacman -S chromium
* sudo pacman -S obs-studio
* sudo pacman -S firefox

That's it you are good for using pure ArchLinux!Enjoy!

Thank you!

#gimalaji_blake
