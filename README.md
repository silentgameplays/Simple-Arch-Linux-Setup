# Simple-ArchLinux-Install-Guide

ArchLinux Installation From Scratch UEFI,GUI,Steam,VLC,Libre Office,OBS-STUDIO,flatpak it's a process,but the results are worth it.You will get a fully customizable system where you can make everything work exactly how you want it,sky is the limit!

# 0. Downloading the installation image and creating a bootable device using rufus:

* Download image here via torrent or direct link:https://www.archlinux.org/download/
* Use Rufus http://rufus.ie/ and select GPT and ISO mode for the image
# (Optional) by default all current Arch installation mediums have a guided installer:
* python -m archinstall guided
# Alternative
* archinstall

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

# 5.Installing basic packages and important stuff:
# For Non-LTS kernel(rolling)
* pacstrap /mnt base base-devel linux linux-headers linux-firmware nano vim dhcpcd networkmanager iw wpa_supplicant dialog network-manager-applet
# (Optional)
# For LTS kernel (Long Term Support)
* pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-firmware nano vim dhcpcd networkmanager iw wpa_supplicant dialog network-manager-applet
# (Optional)
# For both Non-LTS and LTS(have both options)
* pacstrap /mnt base base-devel linux linux-headers linux-lts linux-lts-headers linux-firmware nano vim dhcpcd networkmanager iw wpa_supplicant dialog network-manager-applet

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

# Uncomment the settings in sudo file and add the main user to sudo(your username):

# Lines to uncomment:
* sudo=ALL(ALL) ALL
* ALL=ALL(ALL) ALL
* wheel=ALL(ALL) ALL
# Add yourself to the sudoers file under sudo:
* username=ALL(ALL) ALL
# Save changes to the sudoers file:
* :w! + Enter to exit and write changes
* :q + Enter exit
# edit sudoers file with nano anytime:
* sudo nano /etc/sudoers 

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

* sudo pacman -S alsa alsa-utils pulseaudio pulseaudio-alsa pavucontrol

# 27.(Optional) Install another terminal and firewall:

* sudo pacman -S lxterminal
* sudo pacman -S ufw

# 28. Install Deepin/Gnome/XFCE/KDE/Cinnamon/LXDE/LXQt desktop environments(choose one): 

# Xfce (compatible display managers sddm)

* sudo pacman -S xfce4 xfce4-goodies gvfs

# To make desktop look great

* sudo pacman -S arc-gtk-theme
# in case you missed it:
* pacman -S networkmanager network-manager-applet
* sudo pacman -S pavucontrol

# Disable tearing in videogames on xfce:

* xfconf-query -c xfwm4 -p /general/use_compositing -s false

# Check if compositing is enabled in xfwm4, you can run :

* xfconf-query -c xfwm4 -p /general/use_compositing

# Deepin (compatible display managers lightdm)

* sudo mkdir home 
* sudo pacman -S deepin deepin-extra

# Gnome(compatible display managers gdm/lightdm)

* sudo pacman -S gnome gnome-extra

# KDE (compatible display managers sddm)
# (Full)
* sudo pacman -S plasma kde-applications
# (Essential)
* sudo pacman -S plasma-meta
# (Minimalist)
* sudo pacman -S plasma-desktop
# Or better minimalist and more up to date
* sudo pacman -S plasma dolphin packagekit-qt5 konsole

# For missing backends on KDE:
* sudo pacman -S packagekit-qt5

# Cinnamon (compatible display managers gdm/lightdm):

* sudo pacman -S cinnamon

# MATE (compatible display managers gdm/lightdm)

* sudo pacman -S mate mate-extra

# LXDE (compatible display managers sddm/lxdm)

* sudo pacman -S lxde

# LXQt (compatible display managers sddm)

* sudo pacman -S lxqt  breeze-icons

# For all DE's after installing create user directories:
* sudo pacman -S xdg-user-dirs
* xdg-user-dirs-update

# NB!:Removing anything can be done by:

* sudo pacman -Rscn application name

# Clearing cache

* sudo pacman -Scc
* sudo pacman -Sc

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

# For Nvidia Non-LTS (rolling)
* sudo pacman -S nvidia nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader 

# For Nvidia LTS(Long Term Support)
* sudo pacman -S nvidia-lts nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader 

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
* sudo pacman -S openra
* sudo pacman -S scummvm
* sudo pacman -S pscx2
* sudo pacman -S retroarch
* sudo pacman -S qbittorrent
* sudo pacman -S shotcut
* sudo pacman -S libretro
* sudo pacman -S dosbox
* sudo pacman -S gnome-multi-writer

# Installing AUR helper yay
* sudo pacman -S git

# NB no sudo!
* git clone https://aur.archlinux.org/yay.git

* cd yay

* makepkg -si

# Installing DXVK for DX10/DX11 conversion support:

* yay -S dxvk-bin

# Installing benchmarking tool phoronix

* yay -S phoronix-test-suite

# Installing gamehub,ecwolf,gzdoom,itch,teamviewer,some games (optional)

* yay -S gamehub 
* yay -S ecwolf
* yay -S gzdoom
* yay -S itch
* yay -S teamviewer
* sudo systemctl enable teamviewerd.service
* sudo systemctl start teamviewerd.service
* yay -S dunelegacy
* yay -S sdlpop 

# Install a bunch of dependencies to make life sort of easier(optional):
* sudo pacman -S lib32-gnutls lib32-libldap lib32-libgpg-error lib32-libxml2 lib32-alsa-plugins lib32-sdl2 lib32-freetype2 lib32-dbus lib32-libgcrypt libgcrypt
* sudo pacman -S -S lib32-sdl lib32-sdl2 lib32-sdl_mixer lib32-sdl_ttf sdl2 sdl_gfx sdl2_image sdl2_mixer sdl2_net sdl2_ttf sdl_ttf mpg123 lib32-mpg123
* sudo pacman -S lib32-gnutls lib32-libldap lib32-libgpg-error lib32-libxml2 lib32-alsa-plugins lib32-sdl2 lib32-freetype2 lib32-dbus lib32-libgcrypt 
* sudo pacman -S libgcrypt openal 
* sudo pacman -S lib32-openal gtk3 gtk4 qt6 lib32-fluidsynth fluidsynth
* sudo pacman -S ark lrzip lzop p7zip unarchiver unrar 
* sudo pacman -S gvfs-mtp neofetch spectacle
* sudo pacman -S gimp shotcut obs-studio

# 35. Update the whole system using:
* sudo pacman -Syu
* sudo pacman -Syyuu
# (Optional) Check the history of CLI:
* history

# 36.(Optional) Clear terminal by using:
* clear

# 37.Auto-mounting drives the easy way:

* sudo pacman -S gnome-disk-utility

# Automount using gnomedisk:
* Edit mount options
* Add this line: 
* nosuid,nodev,nofail,x-gvfs-show,auto
# show system status
* sudo systemctl status
# refresh system databases in case something breaks
* sudo pacman -Syyuu
# full system update
* sudo pacman -Syu
# (Optional) backup of arch using timeshift with yay
* yay timeshift 
* link to AUR https://aur.archlinux.org/packages/timeshift/
# Cleaning up dependencies,cache and other stuff

* sudo pacman -Sc
* sudo pacman -Scc
* sudo du -sh ~/.cache/
* rm -rf ~/.cache/*
* sudo pacman -Qtdq
* sudo pacman -Rns $(pacman -Qtdq)
* yay -Scc

# Recursively removing orphans when cluttered 
* su
* pacman -Qtdq | pacman -Rns -
* exit
# if there are no orphans left:error: argument '-' specified with empty stdin
# Removing everything but essential packages
* sudo pacman -D --asdeps $(pacman -Qqe)
# This will remove evrything including Desktop Environment and drivers,except for core essential stuff,use as a last resort or if you like tinkering.
# Change the installation reason to "as explicitly" of only the essential packages, those you do not want to remove, in order to avoid targeting them: 
* sudo pacman -D --asexplicit base linux linux-firmware

# 38 Creating a bootable Windows 10 USB using Disks utility (Possible on any linux distro even without GNOME)
* Download a Windows image from MS link below:
* https://www.microsoft.com/en-us/software-download/windows10
* Insert USB Drive
* Launch Disks Utility
* Select your USB Drive and in the top right=corner click the menu select Format Disk
* In Partitioning select Compatible with modern systems and hard disks>2TB (GPT)
* Click Format wait for it to finish 
* Click Partition>For Use with Windows(NTFS) (in Volume labele type Windows or ESD)
* Mount the USB and Open it
* Go to the place where you downloaded Windows 10 ISO and select Open with Disk Image Mounter
* Open Copy everything from the Windows 10 ISO and paste into your USB Drive,wait for it to finish(takes a while)

# (Optional) Installing KVM an QEMU
* LC_ALL=C lscpu | grep Virtualization
* zgrep CONFIG_KVM /proc/config.gz
* sudo pacman -S virt-manager qemu vde2 ebtables dnsmasq bridge-utils openbsd-netcat
* sudo systemctl enable libvirtd.service
* sudo systemctl start libvirtd.service
* sudo nano /etc/libvirt/libvirtd.conf 

# Uncomment for usage with your user
* unix_sock_group = "libvirt"
* unix_sock_rw_perms = "0770"

# Run these commands
* sudo usermod -a -G libvirt $(whoami)
* sudo systemctl restart libvirtd.service

# NB if having trouble with GNOME Disks Utility recognizing the NTFS file format
* sudo pacman -S ntfs-3g
# (optional) Install Nvidia shadowplay for obs on Arch
* # Get nvidia-patch: https://github.com/keylase/nvidia-patch
* Extract and go to the folder
* Open in terminal
* sudo ./patch-fbc.sh
* # Get obs-nvfbc: https://gitlab.com/fzwoch/obs-nvfbc
* Extract and go to folder
* Open in terminal 
* yay -S libgl-dev libobs-dev libsimde-dev meson ninja-build
* meson build
* ninja -C build
* Go back to GUI and copy nvfbc.so
* Enable hidden files and fodlers
* Go to /home/user/.config/obs-studio
* Create the following folders plugins->nvfbc->bin->64bit
* paste nvfbc.so into 64bit
* Go to OBS Studio and add the NvFBC Source to your scene
# NB! Optional (recommended) disabling kernel and driver updates for more stable experience
* sudo nano /etc/pacman.conf
# Uncomment
* #IgnorePkg =
# It should look like this for kernel and driver and for example firefox:
* IgnorePkg = linux, nvidia, firefox
# For kernel only:
* IgnorePkg = linux

That's it you are good for using pure ArchLinux and don't forget to view archwiki for more advanced stuff:
https://wiki.archlinux.org/
and don't forget to install and use 
* sudo pacman -S man
$ man <your command here>
example man pacman
Enjoy!

Thank you!

#gimalaji_blake
#Silent Gameplay
* My YouTube!
* https://www.youtube.com/c/SilentGameplays/
* Small video on how to install ArchLinux with UEFI:
* https://youtu.be/5mNEMWPVcec
* Enjoy!
