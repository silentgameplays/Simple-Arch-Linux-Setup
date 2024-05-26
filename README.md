# Simple-ArchLinux-Install-Guide

ArchLinux Installation From Scratch UEFI,GUI,Steam,VLC,Libre Office,OBS-STUDIO,flatpak it's a process,but the results are worth it.You will get a fully customizable system where you can make everything work exactly how you want it,sky is the limit!

# 0. Downloading the installation image and creating a bootable device using rufus or gnome-multi-writer utility:

* Download image here via torrent or direct link:https://www.archlinux.org/download/
* Use Rufus http://rufus.ie/ and select GPT and ISO mode for the image
* Also Balena Etcher portable is a nice tool https://www.balena.io/etcher/
* On Linux use gnome-multi-writer utility or ventoy 
# By default all current Arch installation mediums have a guided installer(better to use manual installation):
* archinstall
# Alternative
* python -m archinstall guided

# Formatting the SSD/HDD properly before installing Arch Linux or any other Linux distribution or operating system:
* sudo cfdisk /dev/sda 
* sudo cfdisk /dev/nvme0n1
* Delete everything you see then Write>>Yes
# Wipping the partition schemes 
* sudo wipefs -a /dev/sda
* sudo wipefs /dev/nvme0n1
# Deleting everything properly so no forencisc recovery is possible
* sudo shred -f -v /dev/sda
* sudo shred -f -v /nvme0n1
# Also this works but it does not show the process:
* sudo shred /dev/sda
* sudo shred /dev/nvme0n1

# NB!! when you are running into weird pgp errors during pacstrap,don't forget to run this command from the Arch ISO!:
* pacman -Sy archlinux-keyring

# 1.Creating partitions using cfdisk(easiest):

# Run this command and delete everything,from free space create the following:

* cfdisk 
* /dev/sdx1 512M  Fat32(EFI)

# NB!The size of the EFI must be 512MB and type should be EFI

# (Optional) Swap patition size can be around 3-10GB 

* /dev/sdx2 8-10GB swap

# Main partition,remaining size can be as large as you want it to be: 

* /dev/sdx3 200GB ext 4

# 2.Formatting the drives:

# For the /dev/sdx1 use the following command:

* mkfs.fat -F32 /dev/sdx1

# (Optional) For the swap /dev/sdx2 use the following commands:

*  mkswap /dev/sdX2
*  swapon /dev/sdX2

# For the primary partition use the following command:

* mkfs.ext4 /dev/sdX3

# 3.Mounting the drives:

* mount /dev/sdX3 /mnt

# 4 Checking if everything worked:

* lsblk
* ip link

# 5.Installing basic packages and important stuff with wifi you will need the iw wpa_supplicant dialog packages:

# For Non-LTS kernel(rolling) with Wi-Fi
* pacstrap /mnt base base-devel linux linux-headers linux-firmware nano vim networkmanager iw wpa_supplicant dialog 

# Non-LTS kernel(rolling) without Wi-Fi
* pacstrap /mnt base base-devel linux linux-headers linux-firmware nano vim networkmanager

# (Optional)

# For LTS kernel (Long Term Support) with Wi-Fi
* pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-firmware nano vim networkmanager iw wpa_supplicant dialog

# LTS Without Wifi
* pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-firmware nano vim networkmanager

# (Optional)

# For both Non-LTS and LTS(have both options) with
* pacstrap /mnt base base-devel linux linux-headers linux-lts linux-lts-headers linux-firmware nano vim dhcpcd networkmanager iw wpa_supplicant dialog 

# Or if you hate networkmanager,but still need Wi-Fi:
* pacstrap /mnt base base-devel linux linux-headers linux-lts linux-lts-headers linux-firmware nano vim dhcpcd iwd iw wpa_supplicant dialog netctl 

# 6. Generating fstab file:

* genfstab -U /mnt >> /mnt/etc/fstab


# 7.Switching to root.All the below commands must be used as root!

* arch-chroot /mnt /bin/bash

# 8.Setting locales:

# A proper way to do it is to find this lines en_US.UTF-8 in locale.gen file and uncomment them:

* nano /etc/locale.gen
* locale-gen

# 9.Adding a main user:

* useradd -g users -G power,storage,wheel -m test

# 10.Assigning passwords for root and main user

# Password for root:

* passwd 

# Password for main user:

* passwd your_new_user

# 11.Editing the sudoers file:

* visudo

# Or

* nano /etc/sudoers

# Ctrl+O to save,Ctrl+ X to exit after chages with nano.

# Uncomment the settings in sudo file and add the main user to sudo(your username):

# Lines to uncomment:
* sudo=ALL=(ALL:ALL) ALL

# (Optional) Add yourself to the sudoers file under root(not recommended):

* username=ALL=(ALL:ALL) ALL

# (Optional) Add yourself to sudoers file under sudo (safer option):

* sudo=ALL=(ALL:ALL) ALL
* username=ALL=(ALL:ALL) ALL

# More secure way by uncommenting the following lines,without touching anything else:

* Defaults targetpw
* ALL ALL=(ALL:ALL)

# Save changes to the sudoers file:
* :w! + Enter to exit and write changes
* :q + Enter exit

# edit sudoers file with nano anytime:

* sudo nano /etc/sudoers 

# 12.Grub and efi tools installation(very important step!):

# Fuse 2 support (older option)

* pacman -S grub efibootmgr dosfstools os-prober mtools fuse2 

# Fuse 3 support (newer option)

* pacman -S grub efibootmgr dosfstools os-prober mtools fuse3

# 13. Creating efi boot directory on the EFI partition:

* mkdir /boot/efi

# 14. Mounting the FAT32 EFI partition:

* mount /dev/sdx1 /boot/efi  

# 15. Grub installation and configuration(very important,otherwise system will not boot!): 

* grub-install --target=x86_64-efi   --bootloader-id=grub --efi-directory=/boot/efi 

* grub-mkconfig -o /boot/grub/grub.cfg

# 16.Additional measures so that the boot does not become unbootable:

* mkdir /boot/efi/EFI/BOOT
* cp /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/BOOT/BOOTX64.EFI

# 17.Exiting chroot and reboot:

* exit

# Unmounting the live partitions and rebooting:

* umount -R /mnt
* reboot

# 18.The easy way to activate the wired connection using network manager.

* sudo systemctl enable NetworkManager.service
* sudo systemctl enable wpa_supplicant.service
* sudo systemctl start NetworkManager.service
* sudo reboot
* sudo nmtui

# Select the desired wired connection it should be activated by default if not activate it
* ping archlinux.org

# 19.For Wi-Fi(Optional):
# NB! Don't enable dhcpcd.service with Network Manager!

* sudo systemctl enable NetworkManager.service
* sudo systemctl start NetworkManager.service
* sudo systemctl enable wpa_supplicant.service
* sudo systemctl start wpa_supplicant.service
* sudo reboot

# Activate the desired Wi-Fi there by entering the password
* sudo nmtui
* ping archlinux.org 

# (Optional) instead of networkmanager enable and start dhcpcd and netctl with wpa_supplicant:

* sudo systemctl enable netctl.service
* sudo systemctl start netctl.service
* sudo systemctl enable dhcpcd.service
* sudo systemctl start dhcpcd.service
* sudo systemctl enable wpa_supplicant.service
* sudo systemctl start wpa_supplicant.service

# 20.Setting date,region and time,use these commands:

* sudo timedatectl set-ntp true
* sudo timedatectl list-timezones
* sudo timedatectl set-timezone Zone/SubZone
* sudo hwclock --systohc
* sudo ln -sf /usr/share/zoneinfo/Region/City /etc/localtime

# Setting the hostname

* sudo hostnamectl set-hostname myhostname

# 21.FOR Intel CPU's install Intel firmware kernel support driver  it is important:
* sudo pacman -S intel-ucode

# FOR AMD CPU's install AMD firmware kernel support driver  it is important:
* sudo pacman -S amd-ucode

# 22. Install Xorg(Wayland still works wonky on NVIDIA): 
* sudo pacman -S xorg xorg-server xorg-xinit xorg-apps xterm xorg-xrandr

# (Optional) for noveau drivers 

* xf86-video-vesa mesa

# 23.(Older) Install audio alsa/pulseaudio drivers and utilities:

* sudo pacman -S alsa-firmware alsa-utils pulseaudio pulseaudio-alsa pavucontrol

# (Newer)Install audio alsa/pipewire drivers and utilities

* sudo pacman -S alsa-firmware alsa-utils pipewire pipewire-alsa pipewire-pulse pavucontrol

# 24.(Optional) Install another terminal and firewall:

* sudo pacman -S lxterminal
* sudo pacman -S ufw

# 25. Installing GNOME/KDE PLASMA/XFCE/Cinnamon/LXDE/LXQt desktop environments(choose one): 

# XFCE (compatible display managers sddm or gdm or lightdm,choose one)

# XFCE with extras
* sudo pacman -S xfce4 xfce4-goodies gvfs

# (Optional) To make desktop look great

* sudo pacman -S arc-gtk-theme
* sudo pacman -S papirus-icon-theme
* sudo pacman -S noto-fonts noto-fonts-emoji

# After the AUR is installed and enabled:
* yay -S numix-icon-theme-git
* yay -S apple-fonts

# in case you need it:
* pacman -S network-manager-applet

# If you want to make your XFCE look better with more custom themes:

* Go to your /home/user/ directory
* Enable view hidden files and folders in Thunar
* Create two directories .themes  and .icons
* Download custom themes here: 
* https://www.xfce-look.org/browse/ 
* Extract and copy/paste into the .theme and .icons folders
* Change in Appearance Settings

# Disable tearing in videogames on xfce:

* xfconf-query -c xfwm4 -p /general/use_compositing -s false

# Check if compositing is enabled in xfwm4, you can run :

* xfconf-query -c xfwm4 -p /general/use_compositing

# Deepin (compatible display managers lightdm)

* sudo mkdir home 
* sudo pacman -S deepin deepin-extra

# Gnome(compatible display managers gdm/lightdm)

* sudo pacman -S gnome gnome-extra
* sudo pacman -S gnome-packagekit

# KDE PLASMA(compatible display managers sddm)

# (Full)
* sudo pacman -S plasma kde-applications

# (Essential)
* sudo pacman -S plasma-meta

# (Minimalist)
* sudo pacman -S plasma-desktop

# Or better minimalist and more up to date
* sudo pacman -S plasma dolphin dolphin-plugins packagekit-qt6 konsole

# For missing backends on KDE Plasma:
* sudo pacman -S packagekit-qt6

# Cinnamon (compatible display managers gdm/lightdm):

* sudo pacman -S cinnamon

# MATE (compatible display managers gdm/lightdm)

* sudo pacman -S mate mate-extra

# LXDE (compatible display managers sddm/lxdm)

* sudo pacman -S lxde

# LXQt (compatible display managers sddm)

* sudo pacman -S lxqt  breeze-icons

# 26. For all DE's after installing create user directories:
* sudo pacman -S xdg-user-dirs
* xdg-user-dirs-update

# NB!:Removing anything can be done by:

* sudo pacman -Rscn application name

# 27. Clearing cache

* sudo pacman -Scc
* sudo pacman -Sc

# 28. Enable the GUI desktop to start at launch via the required display manager: 

# SDDM with customizable settings:

* sudo pacman -S sddm sddm-kcm
* sudo systemctl enable --now sddm 

# LIGHTDM with customizable settings:

* sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings
* sudo systemctl enable --now lightdm 

# GDM with customizable settings:
* sudo pacman -S gdm libgdm
* sudo systemctl enable --now gdm

# (Longer way) In case LIGHTDM is missing: 

* sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings
* sudo systemctl enable lightdm.service
* sudo systemctl start lightdm.service

# In case GDM is missing:

* sudo pacman -S gdm libgdm
* sudo systemctl enable gdm.service
* sudo systemctl start  gdm.service

# In case SDDM is missing:

* sudo pacman -S sddm sddm-kcm
* sudo systemctl enable sddm
* sudo systemctl start sddm

# For LXDE (optional):

* sudo pacman -S lxdm

# or for gtk3
* sudo pacman -S lxdm-gtk3
* sudo systemctl enable lxdm
* sudo systemctl start  lxdm

# (Optional) For XFCE you might want to enable autologin in the conf file:

* sudo nano /etc/sddm.conf.d/autologin.conf

* [Autologin]
* User=test
* Session=default

# (Optional) Use this theme for better icons on all DE's:

* sudo pacman -S papirus-icon-theme
* sudo pacman -S noto-fonts noto-fonts-emoji
* sudo pacman -S arc-gtk-theme

# 29. Edit the pacman conf file to enable mirror list to enable multilib support(for 32bit) for Steam and proprietary drivers: 

* sudo nano /etc/pacman.conf

# Uncomment these lines:

* [multilib]
* Include = /etc/pacman.d/mirrorlist

# Update pacman libraries and system:

* sudo pacman -Syu
* sudo pacman -Syyuu

# 30. Install NVIDIA or AMD proprietary drivers and utilities last!:

# For Nvidia Non-LTS (rolling)
* sudo pacman -S nvidia nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers 

# For Nvidia LTS(Long Term Support)
* sudo pacman -S nvidia-lts nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers 

# NVIDIA DRM Wayland Support and generally improved gaming support on X11 as well With RTX Cards:

# Edit /etc/mkinitcpio.conf

* sudo nano /etc/mkinitcpio.conf
  
# * Change MODULES=()
# * To MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)

* sudo mkinitcpio -P
  
# Edit GRUB /etc/default/grub for other bootloaders check the Arch Wiki https://wiki.archlinux.org/title/Kernel_module#Setting_module_options

* sudo nano /etc/default/grub
  
# * Change GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet"
# * To GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet nvidia_drm.modeset=1"

* sudo grub-mkconfig -o /boot/grub/grub.cfg

# to check if everything works:
* sudo cat /sys/module/nvidia_drm/parameters/modeset


* sudo pacman -S egl-wayland ibglvnd
  
# (Optional) Nvidia dkms for Zen/Hardened kernels:

* sudo pacman -S nvidia-dkms

# If you are experiencing NVIDIA Driver Errors with latest kernels or after updates run this:

* sudo mkinitcpio -P

# Reboot for changes to take effect,if using X11 as main:

* sudo reboot

# To disable screen tearing in games and general performance improvement on NVIDIA GPU's,login as root(su) for X11 no such thing for Wayland:

* In Nvidia Settings set Force Composition Pipeline

# Run this to improve perdormance:

* sudo nvidia-xconfig

# Drivers For AMD, no need to tinker with DRM
* sudo pacman -S mesa lib32-mesa mesa-vdpau lib32-mesa-vdpau lib32-vulkan-radeon vulkan-radeon glu lib32-glu vulkan-icd-loader lib32-vulkan-icd-loader
* sudo reboot

# 31. Install Steam 
* sudo pacman -S steam

# 32. Installing AUR helper yay
* sudo pacman -S git

# NB No sudo!

* git clone https://aur.archlinux.org/yay.git
* cd yay
* makepkg -si

# 33. (Optional)Installing other stuff:
# Browsers 
* sudo pacman -S firefox-developer-edition
* sudo pacman -S chromium
* sudo pacman -S firefox
* yay -s brave-bin

# LibreOffice rolling:
* sudo pacman -S libreoffice-fresh

# OR stable:
* sudo pacman -S libreoffice-still

# Other stuff,inlcuding OBS Studio.
* yay -S dhewm3-git
* sudo pacman -S obs-studio
* sudo pacman -S flatpak
* sudo pacman -S lutris
* sudo pacman -S openra
* sudo pacman -S gimp
* sudo pacman -S krita
* sudo pacamn -S kate

# Torrent clients:

* sudo pacman -S qbittorrent
* sudo pacman -S ktorrent
* sudo pacman -S transmission-qt

# OR for GTK based DE's (XFCE/GNOME)

* sudo pacman -S transmission-gtk

# Video Editors:

* sudo pacman -S kdenlive
* sudo pacman -S shotcut

# Emulators:

* sudo pacman -S libretro
* sudo pacman -S dosbox
* sudo pacman -S scummvm
* sudo pacman -S pscx2
* sudo pacman -S retroarch

# Optimization for gaming gamemode and gamescope:

* sudo pacman -S gamemode lib32-gamemode
* sudo pacman -S gamescope 

# (Optional) increase max vm.max_map_count to prevent more demanding games from crashing:

* sudo nano /usr/lib/sysctl.d/10-arch.conf
* change vm.max_map_count=2147483642
* reboot
* cat /proc/sys/vm/max_map_count 


# OpenMW and other mods

* sudo pacman -S openmw
* yay -S ecwolf
* yay -S gzdoom
* yay -S itch

# Teamviewer from AUR:

* yay -S teamviewer
* sudo systemctl enable teamviewerd.service
* sudo systemctl start teamviewerd.service

# More mods,games and tools:

* sudo pacman -S supertux
* sudo pacman -S supertuxkart
* yay -S dunelegacy
* yay -S sdlpop 
* sudo pacman -S 0ad
* sudo pacman -S xonotic
* sudo pacman -S wesnoth
* yay -S freedm
* sudo pacman -S openra
* sudo pacman -S freeciv
* sudo pacman -S dwarffortress
* flatpak install flathub com.moddb.TotalChaos
* yay -S opensurge
* yay -S commander-genius-git

# USB writers:

* yay -S ventoy
* sudo pacman -S gnome-multi-writer

# Partition managers:

* sudo pacman -S gnome-disk-utility
* sudo pacman -S partitionmanager
* sudo pacman -S gparted

# Installing DXVK for DX10/DX11 conversion support:

* yay -S dxvk-bin

# Installing benchmarking tool phoronix

* yay -S phoronix-test-suite

# Install a bunch of dependencies/packages to make life sort of easier(Optional):
* sudo pacman -S gtk3 gtk4 qt6 gvfs 

# Archiver tool for Plasma:
* sudo pacman -S ark lrzip lzop p7zip unarchiver unrar 

# Archiver tools for GNOME,for XFCE both ark and file-roller can be used:
* sudo pacman -S file-roller nemo-fileroller unzip lzop p7zip unrar unarchiver


# Dependencies multimedia/libraries(OPTIONAL if you want full no bloatware system skip these,or choose the ones you need):

* sudo pacman -S fluidsynth lib32-fluidsynth openal lib32-openal gvfs gvfs-nfs libkate gst-plugins-base gst-plugins-bad-libs gst-libav lib32-gst-plugins-good gst-plugin-gtk lib32-gstreamer lib32-gst-plugins-base lib32-gst-plugins-base-libs xvidcore lib32-libxvmc libxvmc ffmpeg gst-libav gst-plugins-good gst-plugins-bad smpeg faac sndio libnma openresolv x264 x265 opus sane lame libao wavpack libmad a52dec libvorbis  faad2  libmpeg2 libtheora libvpx libde265 libdv schroedinger dav1d rav1e gst-libav gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly gst-plugin-pipewire lib32-pipewire pipewire-zeroconf flac lib32-flac smpeg lib32-smpeg mac opus lib32-opus opus-tools opusfile libmpeg2 

# (Optional) Media players (pick one or two or all or what works best):

* sudo pacman -S  mpv vlc dragon kmplayer celluloid mpd qtav

# (Optional) In case you want a GNOME-desktop package on other DE(might solve some issues/might cause some issues):

* sudo pacman -S gnome-desktop

# (In case you are using meson builds)

* sudo pacman -S meson


# (Optional)Install wine and some more packages-dependencies for gaming:

* sudo pacman -S wine wine-mono wine-gecko lutris steam
* sudo pacman -S mono  
* sudo pacman -S lib32-v4l-utils lib32-libxcomposite lib32-opencl-icd-loader lib32-gst-plugins-base-libs 

* sudo pacman -Syyuu

* sudo pacman -S opusfile

* sudo pacman -S gcc-libs grilo grilo-plugins


# (Optional) More packages-dependencies for Wine,Dosbox,Scummvm for older games:

* sudo pacman -S mingw-w64 glslang lib32-libvorbis 
* sudo pacman -S  dosbox scummvm 
* yay -S dxvk


# (Optional) Install Glourious Eggroll Proton GE the easy way:
 * Download the latest release here: https://github.com/GloriousEggroll/proton-ge-custom/releases
 * Extract,enable hidden files and folders 
 * Create a folder in your /home/user/steam/root/compatibilitytools.d if it does not exist.
 * Copy/paste the extracted GE folder into /home/user/config/.steam/root/compatibilitytools.d
 * Restart Steam,enjoy the custom GE build
 
# (Optional) Install spectacle for screenshots (KDE PLASMA) and neofetch to have "I use arch btw" in your CLI for screenshots and reddit,also man pages support to actually learn some stuff about what packages you install and flatpak for a wide variety of software,most of which you can find in AUR.

 * sudo pacman -S spectacle neofetch flatpak man
 
 * sudo pacman -S sonnet

 * sudo pacman -S enchant

 * sudo pacman -Syyuu

# (Optional) Gnome multi-writer tool, useful for creating usb sticks with OS installs and gnome-boxes for vm's useful GUI for QEMU:

 * sudo pacman -S gnome-multi-writer
 * sudo pacman -S gnome-boxes
 * sudo pacman -S virt-manager qemu-desktop
 
 # To use virt manager isntead of GNOME Boxes properly run: 
 
 * sudo systemctl enable libvirtd.service
 * sudo systemctl start libvirtd.service
 
 # OR 
 
 * sudo systemctl enable --now libvirtd.service
 
# Check this website for additional driver/video support on vm's for both GNOME BOXES and virtmanager:
* https://www.spice-space.org/download.html

# (Optional) install itch for indies games and teamviewer:

 * yay -S itch teamviewer

# Enable teamviewerd.service when you see scary messages

 * sudo systemctl enable teamviewerd.service

 * sudo systemctl start teamviewerd.service

# (Optional) Install neofetch and spectacle if you need them:

 * sudo pacman -S gvfs-mtp neofetch spectacle

# 34. Update the whole system using:

* sudo pacman -Syu

# OR

* sudo pacman -Syyuu

# (Optional) Check the history of CLI:

* history

# 35.(Optional) Clear terminal by using:

* clear

# 36.Auto-mounting drives the easy way:

* sudo pacman -S gnome-disk-utility

# Automount using gnome disk utility:
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

# Cleaning up unused dependencies/orphaned packages,cache and other stuff

* sudo pacman -Sc
* sudo pacman -Scc
* sudo du -sh ~/.cache/
* sudo rm -rf ~/.cache/*
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

# NB! This command will remove everything,including Desktop Environment and drivers,except for core essential stuff,use as a last resort or if you like tinkering for many hours with your system or just want to do a clean install aggain using TTY.

# Change the installation reason to "as explicitly" of only the essential packages, those you do not want to remove, in order to avoid targeting them: 
* sudo pacman -D --asexplicit base linux linux-firmware

# Removes stuff before using via terminal use "man rm" not to break anything
* sudo pacman rm -rf./(name of directory or file) 

# 37. Creating a bootable Windows 10 USB using Disks utility (Possible on any linux distro even without GNOME)
* Download a Windows image from MS link below:
* https://www.microsoft.com/en-us/software-download/windows10
* Insert USB Drive
* Launch Disks Utility
* Select your USB Drive and in the top right=corner click the menu select Format Disk
* In Partitioning select Compatible with modern systems and hard disks>2TB (GPT)
* Click Format wait for it to finish 
* Click Partition> For Use with Windows(NTFS) (in Volume labele type Windows or ESD)
* Mount the USB and Open it
* Go to the place where you downloaded Windows 10 ISO and select Open with Disk Image Mounter
* Open Copy everything from the Windows 10 ISO and paste into your USB Drive,wait for it to finish(takes a while)

# Additional tinkering for optimal gaming experience

# 39. Fixing audio crackling in wine games in pipewire
* sudo nano /usr/share/pipewire/pipewire.conf
# * Change #default.clock.allowed-rates = [ 48000 ]
# * To #default.clock.allowed-rates = [ 44100 48000 ]
* reboot

# 40 (Optional) Installing KVM an QEMU
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


# NB if having trouble with GNOME Disks Utility or any other utility recognizing the NTFS file format
* sudo pacman -S ntfs-3g

# Install and use the donwgrade tool from AUR for any package you want downgraded:
* yay -S downgrade package name
  
# Fix outdated yay or paru issues fast instead of reinstalling them:
* sudo ln -s /usr/lib/libalpm.so.14.0.0 /usr/lib/libalpm.so.13 

# 41. NVIDIA Shadowplay with GPU screen recorder on Arch Linux

# Make sure you have yay or paru installed for AUR:

* yay -S gpu-screen-recorder-git

* yay -S  gpu-screen-recorder-gtk

# (Optional) Get nvidia-patch: https://github.com/keylase/nvidia-patch

* Extract and go to the folder
* Open in terminal
* sudo ./patch-fbc.sh

# NB! Optional (not recommended) disabling kernel and driver updates for more stable experience
* sudo nano /etc/pacman.conf
# Uncomment
* #IgnorePkg =
* #IgnoreGroup = 

# It should look like this for kernel and driver and for example firefox:
* IgnorePkg = linux nvidia firefox
* IgnoreGroup = linux nvidia firefox

# For kernel only:
* IgnorePkg = linux
* IgnoreGroup = linux

# View drivers in use 

* lspci -v
* sudo lspci -v

# (Optional) mkinitcpio:
* sudo mkinitcpio

# That's it you are good for using pure ArchLinux and don't forget to view archwiki for more advanced commands and packages:
https://wiki.archlinux.org/

# Don't forget to install and use man
* sudo pacman -S man
* man (your command here)

# Example: man pacman

# If you start getting errors like  "signature from "John Smith <john.smith@archlinux.org>" is marginal trust" do these steps:
* sudo pacman -S archlinux-keyring
* sudo pacman -Syu  archlinux-keyring
* sudo pacman -Syyuu

# Run  this in case it still occurs:
* sudo pacman-key --refresh-keys
* sudo pacman -Syu archlinux-keyring

## (Optional) Adding a secondary SSD/HDD in fstab manually:

# If it is an existing SSD/HDD that you already formatted with ext4 or btrfs and automounted in filemanager like Dolphin or Nemo,then you have to check in properties for example "/media/user/Backup" that is your mount point.

* sudo lsblk -f

* sudo nano /etc/fstab 

# Contents of FSTAB:

# < file system > < mount point >   < type >  < options >                        < dump >  < pass >

* / was on /dev/sda2 during installation
* UUID=362fe9a2-29fc-43fe-824d-09d1d93b1549 /               ext4    errors=remount-ro 0       1
* /boot/efi was on /dev/sda1 during installation
* UUID=64EF-0C84  /boot/efi       vfat    umask=0077      0       1

* swap was on /dev/sda3 during installation
* UUID=b36261ad-191e-4cb5-ba0e-f2715e32f82c none            swap    sw              0       0
* /dev/sr0        /media/cdrom0   udf,iso9660 user,noauto     0       0

# Add your SSD/HDD with its mount point here:
* /dev/sdb1       /media/user/Backup                        ext4    defaults,auto 0      2

 
# Save the changes and exit,reboot,you are good 


# SilentGamePLS Youtube channel:
* https://www.youtube.com/c/silentgamepls

*Enjoy!
Thank you!
#silentgamepls



