# Simple-ArchLinux-Install-Guide

ArchLinux Installation From Scratch UEFI,GUI,Steam,VLC,Libre Office,OBS-STUDIO,flatpak it's a process,but the results are worth it.You will get a fully customizable system where you can make everything work exactly how you want it,sky is the limit!

# 0. Downloading the installation image and creating a bootable device using rufus:

* Download image here via torrent or direct link:https://www.archlinux.org/download/
* Use Rufus http://rufus.ie/ and select GPT and ISO mode for the image

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
* ip link

# If you have wi-fi(optional,use cable):
* wifi-menu

# 5.Installing basic packages and important stuff:

* pacstrap /mnt base base-devel linux linux-firmware nano vim dhcpcd networkmanager iw wpa_supplicant dialog network-manager-applet

# 6. Generating fstab file:

* genfstab -U /mnt >> /mnt/etc/fstab

# 7.Switching to root.All the below commands must be used as root!

* arch-chroot /mnt /bin/bash

# 8.Setting date,region and time,use these commands:

* timedatectl set-ntp true
* timedatectl list-timezones
* timedatectl set-timezone Zone/SubZone
* ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
* hwclock --systohc

# 9.Setting the hostname

* hostnamectl set-hostname myhostname

# 10.Setting locales:
# A proper way to do it is to find this lines en_US.UTF-8 in locale.gen file and uncomment them:
* nano /etc/locale.gen
* locale-gen

# 11.Adding a main user:

* useradd -g users -G power,storage,wheel -m test

# 12.Assigning passwords for root and main user

# Password for root:

* passwd 

# Password for main user:

* passwd your_new_user

# 13.Editing the sudoers file:

* visudo

# Uncomment the needed settings in sudoers file and add the main user into it:

# Lines safe to uncomment :ALL= ALL(ALL) ALL

* :w! + Enter to exit and write changes
* :q + Enter exit

# 14.Grub and efi tools installation(very important step!):

* pacman -S grub efibootmgr dosfstools os-prober mtools fuse2 

# 15. Creating efi boot directory on the EFI partition:

* mkdir /boot/efi

# 16. Mounting the FAT32 EFI partition:

* mount /dev/sdx1 /boot/efi  #Mount FAT32 EFI partition 

# 17. Grub installation and configuration(very important,otherwise system will not boot!): 

* grub-install --target=x86_64-efi   --bootloader-id=grub --efi-directory=/boot/efi 
* grub-mkconfig -o /boot/grub/grub.cfg

# 18.Additional security measures so that the boot does not become unbootable:

* mkdir /boot/efi/EFI/BOOT
* cp /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/BOOT/BOOTX64.EFI

# 19.Creating and editing the efi boot file so that system becomes bootable:

* nano /boot/efi/startup.nsh

* bcf boot add 1 fs0:\EFI\GRUB\grubx64.efi “My grub bootloader”
* exit

# 20.Exiting root and rebboting:

* exit

# 21.Unmounting the live partitions and rebooting:

* umount -R /mnt
* reboot

# 22.The easy way to activate the wired connection using network manager.NB!Don't enable dhcpcd.service in the previous steps it will conflict with the networkmanager!Just ignore it and it will make your life easier:

* sudo systemctl enable NetworkManager.service
* sudo systemctl enable wpa_supplicant.service
* sudo systemctl start NetworkManager.service
* sudo reboot
* sudo nmtui
* # Select the desired wired connection it should be activated by default if not activate it
* ping google.com

# 23.For Wi-Fi(Optional):
# NB! Don't enable dhcpcd.service!
* sudo systemctl enable NetworkManager.service
* sudo systemctl enable wpa_supplicant.service
* sudo systemctl start NetworkManager.service
* sudo reboot
* sudo nmtui
* # Activate the desired Wi-Fi there by entering the password
* ping google.com 

# 24. Install intel firmware it is important:

* sudo pacman -S intel-ucode

# 25. Install Xorg: 

* sudo pacman -S xorg xorg-server xorg-xinit xorg-apps xterm xorg-xrandr

# Optional for noveau drivers 

* xf86-video-vesa mesa

# 26. Install alsa audio drivers and utilities:

* sudo pacman -S alsa alsa-utils pulseaudio pulseaudio-alsa 

# 27.(Optional) Install the terminal:

* sudo pacman -S lxterminal

# 28. Install Deepin/Gnome/XFCE/KDE/Cinnamon/LXDE/LXQt desktop environments(choose one): 

# Xfce (compatible display managers sddm)

* sudo pacman -S xfce4 xfce4-goodies gvfs
# To make desktop look great
* pacman -S networkmanager network-manager-applet
* sudo pacman -S pavucontrol
* sudo pacman -S arc-gtk-theme
# Disable tearing in videogames
* xfconf-query -c xfwm4 -p /general/use_compositing -s false
# Check if compositing is enabled in xfwm4, you can run :
* xfconf-query -c xfwm4 -p /general/use_compositing

# Deepin (compatible display managers lightdm)
* sudo mkdir home 
* sudo pacman -S deepin deepin-extra

# Gnome(compatible display managers gdm/lightdm)

* sudo pacman -S gnome gnome-extra

# KDE (compatible display managers sddm)

* sudo pacman -S plasma kde-applications

# Cinnamon (compatible display managers gdm/lightdm):

* sudo pacman -S cinnamon

# MATE (compatible display managers gdm/lightdm)

* sudo pacman -S mate mate-extra

# LXDE (compatible display managers sddm/lxdm)

* sudo pacman -S lxde

# LXQt (compatible display managers sddm)

* sudo pacman -S lxqt  breeze-icons

# NB!:Removing anything can be done by:

* sudo pacman -Rscn application

# 29. Enable the GUI desktop to start at launch via the required display manager: 

# In case lightdm is missing: 

* pacman -S lightdm
* sudo systemctl enable lightdm.service
* sudo systemctl start lightdm.service

# In case gdm is missing:

* sudo pacman -S gdm
* sudo systemctl enable gdm.service
* sudo systemctl start  gdm.service

# In case sddm is missing:

* sudo pacman -S sddm
* sudo systemctl enable sddm
* sudo systemctl start sddm

# For LXDE (optional):

* sudo pacman -S lxdm
# or for gtk3
* sudo pacman -S lxdm-gtk3
* sudo systemctl enable lxdm
* sudo systemctl start  lxdm

# For XFCE you might want to enable autologin in the conf file:

* sudo nano /etc/sddm.conf.d/autologin.conf

* [Autologin]
* User=test
* Session=default

# 30. Edit the pacman conf file to enable mirror list so we can enable multilib(same as multiarch) for Steam and proprietary drivers: 

* sudo nano /etc/pacman.conf

# Uncomment these lines:

* [multilib]
* Include = /etc/pacman.d/mirrorlist

# 31. Update pacman libraries and system:

* sudo pacman -Syu

# 32. Install NVIDIA or AMD proprietary drivers and utilities last!:

# For Nvidia
* sudo pacman -S nvidia nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader 


# For AMD
* sudo pacman -S mesa lib32-mesa mesa-vdpau lib32-mesa-vdpau lib32-vulkan-radeon vulkan-radeon glu lib32-glu vulkan-icd-loader lib32-vulkan-icd-loader
* sudo reboot

# 33. Install Steam 

* sudo pacman -S steam

# 34. Install VLC and other stuff:

* sudo pacman -S vlc
* sudo pacman -S libreoffice-fresh
* sudo pacman -S chromium
* sudo pacman -S obs-studio
* sudo pacman -S firefox
* sudo pacman -S flatpak
* sudo pacman -S wine
* sudo pacman -S wine-gecko
* sudo pacman -S wine-mono
* sudo pacman -S lutris

# Installing AUR helper yay

* sudo pacman -S git
# NB no sudo!
* git clone https://aur.archlinux.org/yay.git

* cd yay

* makepkg -si

# Installing DXVK for DX10/DX11 conversion support:
* yay -S dxvk-bin

# Making Origin games run on lutris properly require the following dependencies:
* sudo pacman -S lib32-gnutls lib32-libldap lib32-libgpg-error lib32-libxml2 lib32-alsa-plugins lib32-sdl2 lib32-freetype2 lib32-dbus lib32-libgcrypt libgcrypt
# 35. Update the whole system using:
* sudo pacman -Syu
# 36.(Optional) Clear terminal by using:
* clear
# 37.Auto-mounting drives the easy way:
* sudo pacman -S gnome-disk-utility
# Automount using gnomedisk:
* 1.Edit mount options
* 2. Add this line: 
* nosuid,nodev,nofail,x-gvfs-show,auto
# show system status
* sudo systemctl status

That's it you are good for using pure ArchLinux and don't forget to view archwiki for more advanced stuff:
https://wiki.archlinux.org/
Enjoy!

Thank you!

#gimalaji_blake
