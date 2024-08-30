# Arch Linux Manual Installation With GRUB Bootloader Cheat Sheet

# 1. Downloading the installation image and creating a bootable USB device
* Download Arch Linux ISO image here via torrent or direct link:
* https://archlinux.org/download/
* Use Rufus https://rufus.ie/en/ and select GPT and ISO mode for the image
* Balena Etcher is another great tool
* https://etcher.balena.io/
* for creating bootable USB's
# (Optional) On Linux you can use gnome-disk-utility or Ventoy

# 2. Before installing Arch Linux on bare metal it is recommended to remove previous filesystems and data, after backing up what the user needs

# wipefs can be used to erase existing signatures on a device:

# For regular SSD's/HDD's

* sudo wipefs -a /dev/sda

# For NVME SSD's

* sudo wipefs /dev/nvme0n1

# (Opional) Deleting everything with shred command properly while overwriting so no forensics recovery is possible, also helps fix bad blocks in some cases:

# For regular SSD's/HDD's

* sudo shred -f -v /dev/sda

# For NVME SSD's

* sudo shred -f -v /nvme0n1

# 3. Using cfdisk to format and create paritions on SSD/HDD before installing Arch Linux

# For regular SSD's/HDD's

* sudo cfdisk /dev/sda

# For NVME SSD's

* sudo cfdisk /dev/nvme0n1

# When prompted by cfdisk, delete everything you see then Write>>Yes

# 4. Creating partitions using cfdisk:

# From free space on the SSD/HDD create the following partitions using cfdisk' TUI (Terminal User Interface):

# Required UEFI partition:

# For regular SSD's/HDD's

* /dev/sda1 512M  Fat32(EFI)

# For NVME SSD's

* /dev/nvme01p1 512M  Fat32(EFI)

# The size of the EFI partition must be 512MB and type of the partition needs to be EFI.

# (Optional) SWAP partition size can be around 8-10GB, but it is not required if you have 16GB/32GB or more RAM

# For regular SSD's/HDD's

* /dev/sda2 8-10GB swap

# For NVME SSD's

* /dev/nvme0n1p2 8-10GB swap

# Required main partition for your main user /mnt, remaining size can be as large as you want it to be:

# For regular SSD's/HDD's

* /dev/sda3 1000GB ext4

# For NVME SSD's

* /dev/nvme0n1p3 1000GB ext4

# Save changes in cfdisk, by using Write option in the cfdisk TUI and exit cfdisk.

# 5.Format partitions,create UEFI partition, mount point partition (main user partition) and swap partition

# UEFI partition

# For regular SSD's/HDD's

* mkfs.fat -F32 /dev/sda

# For NVME SSD's

* mkfs.fat -F32 /dev/nvme0n1p1

# SWAP partition

# For regular SSD's/HDD's

* mkswap /dev/sda2

* swapon /dev/sda2

# For NVME SSD's

* mkswap /dev/nvme0n1p2

* swapon /dev/nvme0n1p2

# Primary partition

# For regular SSD's/HDD's

* mkfs.ext4 /dev/sda3

# For NVME SSD's

* mkfs.ext4 /dev/nvme0n1p3

# 6. Mounting the primary partition

# For regular SSD's/HDD's

* mount /dev/sda3 /mnt

# For NVME SSD's

* mount /dev/nvme0n1p3 /mnt

# Check if everything worked by using lsblk command.

# 7.Base Arch Linux installation

# Check if internet works by using the ip link command.

# Standard Arch Linux installation with standard Ethernet and Wi-Fi support,nano as text editor:

* pacstrap /mnt base base-devel linux linux-headers linux-firmware nano networkmanager

# Generate fstab file:

* genfstab -U /mnt >> /mnt/etc/fstab

# (Optional choice) Standard Arch Linux installation with better Wi-Fi support and nano as text editor:

* pacstrap /mnt base base-devel linux linux-headers linux-firmware nano networkmanager iw wpa_supplicant dialog

# Generate fstab file:
* genfstab -U /mnt >> /mnt/etc/fstab

# (Optional choice) LTS(Long Term Support) Arch Linux installation with standard Ethernet and Wi-Fi support,nano as text editor:

* pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-firmware nano networkmanager

# Generate fstab file:

* genfstab -U /mnt >> /mnt/etc/fstab

# (Optional choice) LTS (Long Term Support) Kernel Arch Linux installation with better Wi-Fi support and nano as text editor:

* pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-firmware nano networkmanager iw wpa_supplicant dialog

# Generate fstab file:

* genfstab -U /mnt >> /mnt/etc/fstab

# (Optional choice) Standard Arch Linux installation with LTS (Long Term Support) Kernel as backup and nano as text editor:

* pacstrap /mnt base base-devel linux linux-headers linux-lts linux-lts-headers linux-firmware nano networkmanager iw wpa_supplicant dialog

# Generate fstab file:
* genfstab -U /mnt >> /mnt/etc/fstab

# 8. Basic Arch Linux configuration with chroot

# Log into your installation as chroot:

* arch-chroot /mnt /bin/bash

# Set locales:

* nano /etc/locale.gen

# (Example) For US locale find this line en_US.UTF-8 in locale.gen file and uncomment the line.

# Generate locale

* locale-gen

# Creating and adding a main user:

* useradd -g users -G power,storage,wheel -m user

# Creating passwords for root and main user

# Password for root:

* passwd

# Type in the root password.

# Password for main user:

* passwd user

# Type in the user password.

# 9. Editing the sudoers file:

* visudo

# or

* nano /etc/sudoers

# Uncomment the settings in sudoers file and add the main user to sudo (user):

# Lines to uncomment:

* sudo=ALL=(ALL:ALL) ALL

# Add yourself to sudoers file under sudo

* user=ALL=(ALL:ALL) ALL

# More secure way by uncommenting the following lines,without touching anything else in the sudoers file:

* Defaults targetpw

* ALL ALL=(ALL:ALL)

# Save changes to the sudoers file:

# For vi text editor:

  # :w! + Enter to exit and write changes
  # :q + Enter to exit

# For nano text editor:

# Ctrl+O to save,Ctrl+ X to exit after making changes with nano text editor.

# (Optional) Edit sudoers file with nano anytime:

* sudo nano /etc/sudoers

# 10. GRUB Bootloader Installation and configuration for UEFI boot

# Install the following packages along with GRUB Bootloader for UEFI support:

* pacman -S grub efibootmgr dosfstools os-prober mtools fuse3

# Create an EFI boot directory on the EFI partition:

* mkdir /boot/efi

# Mount the FAT32 EFI partition:

# For regular SSD's/HDD's

* mount /dev/sda1 /boot/efi

# For NVME SSD's

* mount /dev/nvme0n1p1 /boot/efi

# GRUB Bootloader Installation:

* grub-install --target=x86_64-efi --bootloader-id=grub --efi-directory=/boot/efi

# (Optional\Experimental) GRUB Bootloader Installation with --removable command to avoid doing Additional measures step.

* grub-install --target=x86_64-efi --bootloader-id=grub --efi-directory=/boot/efi --removable

# Generating GRUB Bootloader configuration file:

* grub-mkconfig -o /boot/grub/grub.cfg

# Additional measures for boot partition to not become unbootable

* mkdir /boot/efi/EFI/BOOT

* cp /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/BOOT/BOOTX64.EFI

# 11. Exit chroot,unmount Arch Linux ISO and reboot:

* exit

* umount -R /mnt

* reboot

# 12. Desktop Environment installation,enabling the network and sound, Xorg installation,Date/Time configuration

# Ethernet

* sudo systemctl enable --now NetworkManager.service

# Ethernet + WiFi

* sudo systemctl enable --now NetworkManager.service

* sudo systemctl enable --now wpa_supplicant.service

* sudo nmtui

# Select the desired wireless connection,wired connections should be activated by default,connect to Wi-Fi using your password.

# Check if internet works:

* ping archlinux.org

# 13. Set date,region and time.

* sudo timedatectl set-ntp true

* sudo timedatectl list-timezones

* sudo timedatectl set-timezone Zone/SubZone

* sudo hwclock --systohc

* sudo ln -sf /usr/share/zoneinfo/Region/City /etc/localtime

# Hostname configuration

* sudo hostnamectl set-hostname myhostname

# 14. Intel CPU firmware kernel support driver:

* sudo pacman -S intel-ucode

# AMD CPU firmware kernel support driver:
* sudo pacman -S amd-ucode

# 15. Xorg installation,Wayland is provided with all the popular DE's:
* sudo pacman -S xorg xorg-server xorg-xinit xorg-apps xterm xorg-xrandr xdg-user-dirs

# Run:

* xdg-user-dirs-update

# 16. Enable 32-bit support for Steam,Wine,Lutris

* sudo nano /etc/pacman.conf

# Uncomment these lines:

* [multilib] Include = /etc/pacman.d/mirrorlist

# Update:

  * sudo pacman -Syu

# 17. Install audio support

  * sudo pacman -S alsa-firmware alsa-utils pipewire pipewire-alsa pipewire-pulse

# 18. GNOME/KDE Plasma/XFCE/Desktop environments

# XFCE

  * sudo pacman -S xfce4 xfce4-goodies gvfs

# Display Manager:

  * sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings

  * sudo systemctl enable --now lightdm.service

# 19. GNOME

  * sudo pacman -S gnome gnome-extra

# For missing backends on GNOME:

  * sudo pacman -S gnome-packagekit

# Display Manager:

  * sudo pacman -S gdm libgdm

  * sudo systemctl enable --now gdm

# 20. KDE PLasma

  * sudo pacman -S plasma kde-applications

# For missing backends on KDE Plasma:

  * sudo pacman -S packagekit-qt6

# Display Manager:

  * sudo pacman -S sddm sddm-kcm

  * sudo systemctl enable --now sddm


# 21.(Optional) Additional dependencies
# (Optional) for noveau drivers 

* xf86-video-vesa mesa

# 22.(Older) Install audio alsa/pulseaudio drivers and utilities:

* sudo pacman -S alsa-firmware alsa-utils pulseaudio pulseaudio-alsa 


# 23. For all DE's after installing create user directories:
* sudo pacman -S xdg-user-dirs
* xdg-user-dirs-update

# NB!:Removing anything can be done by:

* sudo pacman -Rscn application name

# 24. Clearing cache

* sudo pacman -Scc
* sudo pacman -Sc

# 25. Enable the GUI desktop to start at launch via the required display manager: 

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

# 26. Edit the pacman conf file to enable mirror list to enable multilib support(for 32bit) for Steam and proprietary drivers: 

* sudo nano /etc/pacman.conf

# Uncomment these lines:

* [multilib]
* Include = /etc/pacman.d/mirrorlist

# Update pacman libraries and system:

* sudo pacman -Syu
* sudo pacman -Syyuu

# 27. Install NVIDIA protprietary or AMD open source drivers and utilities last!:

# For Nvidia Non-LTS (rolling)
* sudo pacman -S nvidia nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers 
# Run mkinitcpio after installation and reboot:

* sudo mkinitcpio -P
  
# For Nvidia LTS(Long Term Support)
* sudo pacman -S nvidia-lts nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers

# Run mkinitcpio after installation and reboot:
* sudo mkinitcpio -P

# NVIDIA DKMS Driver For Zen and Multiple Kernels
* sudo pacman -S nvidia-dkms nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers 

# Run mkinitcpio after installation and reboot:
* sudo mkinitcpio -P 

# NVIDIA Open Source Driver
* sudo pacman -S nvidia-open nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers 

# Run mkinitcpio after installation and reboot:

* sudo mkinitcpio -P

# NVIDIA Open Source DKMS Driver
* sudo pacman -S nvidia-open-dkms nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers 

# Run mkinitcpio after installation and reboot:

* sudo mkinitcpio -P 

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

# Extra Steps with GNOME/GDM

# Removing the gdm udev that disables wayland
* sudo ln -s /dev/null /etc/udev/rules.d/61-gdm.rules
* sudo reboot

# Dependencies,usually provided by KDE/GNOME

* sudo pacman -S egl-wayland libglvnd
  
# After NVIDIA Driver updates run:

* sudo mkinitcpio -P

# Reboot for changes to take effect:

* sudo reboot

# Run this to improve perdormance:

* sudo nvidia-xconfig

# Drivers For AMD, no need to tinker with DRM
* sudo pacman -S mesa lib32-mesa mesa-vdpau lib32-mesa-vdpau lib32-vulkan-radeon vulkan-radeon glu lib32-glu vulkan-icd-loader lib32-vulkan-icd-loader
* sudo reboot

# 28. Install Steam 
* sudo pacman -S steam

# 29. Installing AUR helper yay
* sudo pacman -S git

# NB No sudo!

* git clone https://aur.archlinux.org/yay.git
* cd yay
* makepkg -si

# 30.(Optional)Installing other stuff:
# Browsers 
* sudo pacman -S firefox-developer-edition
* sudo pacman -S chromium
* sudo pacman -S firefox
* yay -s brave-bin

# LibreOffice rolling:
* sudo pacman -S libreoffice-fresh

# OR stable:
* sudo pacman -S libreoffice-still

# 31. Other stuff,inlcuding OBS Studio.
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

# 32. Install a bunch of dependencies/packages to make life sort of easier(Optional):
* sudo pacman -S gtk3 gtk4 qt6 gvfs 

# Archiver tool for Plasma:
* sudo pacman -S ark lrzip lzop p7zip unarchiver unrar 

# Archiver tools for GNOME,for XFCE both ark and file-roller can be used:
* sudo pacman -S file-roller nemo-fileroller unzip lzop p7zip unrar unarchiver


# 33. Dependencies multimedia/libraries(OPTIONAL if you want full no bloatware system skip these,or choose the ones you need):

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
# For better wine support
* sudo systemctl restart systemd-binfmt

# (Optional) More packages-dependencies for Wine,Dosbox,Scummvm for older games:

* sudo pacman -S mingw-w64 glslang lib32-libvorbis 
* sudo pacman -S  dosbox scummvm 
* yay -S dxvk
* sudo pacman -S mangohud lib32-mangohud


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
* yay -Sc
* yay -Scc
* sudo rm /var/cache/pacman/pkg/*

# Removing only the stated packages without dependencies:

* sudo pacman -Rdd package name

# Removing only the stated packages with dependencies

* sudo pacman -Rscn package name

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

# 35. Creating a bootable Windows 10 USB using Disks utility (Possible on any linux distro even without GNOME)
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

# 36. Fixing audio crackling in wine games in pipewire
* sudo nano /usr/share/pipewire/pipewire.conf
# * Change #default.clock.allowed-rates = [ 48000 ]
# * To #default.clock.allowed-rates = [ 44100 48000 ]
* reboot

# 37 (Optional) Installing KVM an QEMU
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

# 38. NVIDIA Shadowplay with GPU screen recorder on Arch Linux

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


# silentgameplays Youtube channel:
* https://www.youtube.com/@silentgameplays/

*Enjoy!
Thank you!
# silentgameplays



