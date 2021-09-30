# (Part 1) Manual Simple-ArchLinux-Install-Guide

ArchLinux Installation From Scratch UEFI,GUI,Steam,VLC,Libre Office,OBS-STUDIO,flatpak it's a process,but the results are worth it.You will get a fully customizable system where you can make everything work exactly how you want it,sky is the limit!

# 0. Downloading the installation image and creating a bootable device using rufus or gnome-multi-writer utility:

* Download image here via torrent or direct link:https://www.archlinux.org/download/
* Use Rufus http://rufus.ie/ and select GPT and ISO mode for the image
* On Linux use gnome-multi-writer utility 
# By default all current Arch installation mediums have a guided installer:
* archinstall
# Alternative
* python -m archinstall guided
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
* sudo pacman -Syyuu

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

# Installing gamehub,ecwolf,gzdoom,itch,ventoy,teamviewer,some games (optional)

* yay -S gamehub 
* yay -S ecwolf
* yay -S gzdoom
* yay -S itch
* yay -S teamviewer
* sudo systemctl enable teamviewerd.service
* sudo systemctl start teamviewerd.service
* yay -S dunelegacy
* yay -S sdlpop 
* yay -S ventoy

# Install a bunch of dependencies to make life sort of easier(optional):
* sudo pacman -S lib32-gnutls lib32-libldap lib32-libgpg-error lib32-libxml2 lib32-alsa-plugins lib32-sdl2 lib32-freetype2 lib32-dbus lib32-libgcrypt libgcrypt
* sudo pacman -S lib32-sdl lib32-sdl2 lib32-sdl_mixer lib32-sdl_ttf sdl2 sdl_gfx sdl2_image sdl2_mixer sdl2_net sdl2_ttf sdl_ttf mpg123 lib32-mpg123
* sudo pacman -S lib32-gnutls lib32-libldap lib32-libgpg-error lib32-libxml2 lib32-alsa-plugins lib32-sdl2 lib32-freetype2 lib32-dbus lib32-libgcrypt 
* sudo pacman -S libgcrypt openal 
* sudo pacman -S lib32-openal gtk3 gtk4 qt6 lib32-fluidsynth fluidsynth gvfs lib32-vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d
* sudo pacman -S ark lrzip lzop p7zip unarchiver unrar 
* sudo pacman -S gvfs-mtp neofetch spectacle
* sudo pacman -S gimp shotcut 

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

# (Part 2) Full Automated Archlinux installation with KDE Plasma with minimum bloat.(Better than point and click setup distros,also all the packages are completely optional,besides the drivers,but if you want newer and older games,libraries and shaders support look no further) 
### Full blown automated ArchLinux + KDE installation + dependencies and optional packages ###

# 1.After booting into arch.iso launch

 * archinstall

# 2.This script installation is pretty straightforward,use ext4 for main partition volume,add your main user to the sudoers file when asked,use systemd for boot (grub does not work with proprietary drivers when using archinstall script) for the sound driver select pipewire instead of pulseaudio and some optional packages firefox,libreoffice-fresh,chromium,vlc,git

# 3. Configure the 32bit support in pacman.conf file

* sudo nano /etc/pacman.conf

# Uncomment these lines:

* [multilib]
* Include = /etc/pacman.d/mirrorlist

# Update pacman configuration

* sudo pacman -Syyuu

# 5.Reinstall NVIDIA driver with proper libraries and dependencies for proper video support:

* sudo pacman -S nvidia nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader opencl-nvidia libvdpau vkd3d lib32-vkd3d lib32-libva-vdpau-driver

# 6. Install a bunch of packages-dependencies:

* sudo pacman -S opencl-headers opencl-clhpp lib32-mesa-vdpau lib32-libva-mesa-driver

# Reboot for changes to take effect,use the X11 don't use Wayland under KDE or any other DE,especially for NVIDIA cards!:

* sudo reboot

# 7. Back to the terminal to install a bunch of pacakges and dependencies for multimedia audio/video decoding/encoding with 32bit support:

* sudo pacman -S lib32-gnutls lib32-libldap lib32-libgpg-error lib32-libxml2 lib32-alsa-plugins lib32-sdl2 lib32-freetype2 lib32-dbus lib32-libgcrypt libgcrypt lib32-sdl lib32-sdl2 lib32-sdl_mixer lib32-sdl_ttf sdl2 sdl_gfx sdl2_image sdl2_mixer sdl2_net sdl2_ttf sdl_ttf mpg123 lib32-mpg123 lib32-libpulse lib32-jack

* sudo pacman -S lib32-fluidsynth fluidsynth lib32-gnutls lib32-libldap lib32-libgpg-error lib32-libxml2 lib32-sdl2 lib32-freetype2 lib32-dbus lib32-libgcrypt

* sudo pacman -S openal lib32-openal gtk3 gtk4 qt6  gvfs gvfs-afc gvfs-smb gvfs-gphoto2 gvfs-nfs clang freetds postgresql-libs unixodbc libfbclient mariadb-libs  gvfs-goa ark lrzip lzop p7zip unarchiver unrar gvfs-mtp neofetch spectacle go xdotool w3m libcaca jp2a chafa feh libheif djvulibre  imagemagick-doc libraw libxml2  ocl-icd openexr librsvg lirc aalib libgoom2  libnfs libkate projectm
* sudo pacman -S gst-python gst-plugins-base gst-plugins-bad-libs gst-plugins-bad gst-libav lib32-gst-plugins-good gst-plugin-gtk lib32-gstreamer lib32-gst-plugins-base lib32-gst-plugins-base-libs gstreamermm phonon-qt5-gstreamer qt-gstreamer 

# 8.Optional installation of software in case you missed some:

* sudo pacman -S obs-studio vlc qbittorrent gnome-disk-utility gnome-multi-writer

# 9. Another bunch of packages-dependencies libraries and support for multimedia audio/video decoding/encoding and 32bit support:
* sudo pacman -S libdvdcss qt5-webkit lua52-socket libtiger ttf-dejavu zvbi libgme twolame libdc1394 libavc1394 libdvdcss lirc aalib libgoom2  libnfs libkate projectm exfat-utils dosfstools ntfs-3g  jfsutils flatpak lib32-opencl-nvidia opencl-nvidia libvdpau git xdg-utils gvfs-gphoto2 gphoto2 gvfs-nfs lib32-libiec61883 xvidcore lib32-libxvmc libxvmc ffmpeg ffmpegthumbs gst-libav xdg-desktop-portal-kde xdg-user-dirs

* sudo pacman -S lib32-libxxf86vm libxxf86vm gst-plugins-good gst-plugins-bad

* sudo pacman -S transcode libquicktime smpeg faac sndio

* sudo pacman -S sdl2_image sdl2_net sdl2_ttf sdl2_gfx sdl sdl_net sdl_mixer sdl_sound sdl_gfx sdl_ttf git networkmanager-qt nm-connection-editor

* sudo pacman -S libnma openresolv

* sudo pacman -S python-opengl qt5-webkit qt5-xmlpatterns qt5-websockets  qt5-remoteobjects qt5-serialport python-pyqt5 bash-completion

* sudo pacman -S  qt5-quick3d python-dbus python-gobject ufw gufw libnma libnm lib32-libnm libvoikko nuspell aspell lib32-giflib lib32-libxinerama  lib32-libxslt sane cups samba dosbox scummvm colord perl-term-readkey perl-tk  logrotate ipp-usb tk vtk ladspa-host vamp-host vamp-plugin-sdk libao sdl_image opencv rtaudio rubberband sox mono lua r tcl ocaml  gnome-desktop swh-plugins opencv t1utils dialog gcc-fortran tcl ttf-liberation

# 10.Install wine and some more packages-dependencies for gaming:

* sudo pacman -S wine wine-mono wine-gecko

* sudo pacman -S lib32-v4l-utils lib32-libxcomposite lib32-opencl-icd-loader lib32-gst-plugins-base-libs

* sudo pacman -Syyuu

* sudo pacman -S opusfile

# 11. ASP.NET/DOTNET libraries for Windows games and more C++ support,required by a bunch of API's:

* sudo pacman -S aspnet-runtime aspnet-runtime-3.1 dotnet-sdk dotnet-runtime-3.1 dotnet-sdk-3.1 dotnet-runtime gcc-libs grilo dotnet-host dotnet-targeting-pack-3.1 dotnet-targeting-pack aspnet-targeting-pack aspnet-targeting-pack-3.1

* sudo pacman -S grilo-plugins

* sudo pacman -S lib32-vkd3d vkd3d

# 12. Install yay using git,if you missed installing it see how to below:

* sudo pacman -S git

* git clone https://aur.archlinux.org/yay.git

* cd yay

* makepkg -si

# 13. More packages-dependencies for audio/video multimedia encoding/decoding and dxvk(DirectX to Vulkan API for Windows games)

* sudo pacman -S mingw-w64 glslang

* yay -S dxvk

* sudo pacman -S alsa pavucontrol pipewire-alsa pipewire-jack alsa-utils alsa-card-profiles lib32-alsa-lib lib32-alsa-oss lib32-alsa-plugins alsa-plugins alsa-lib gdal wavpack celt libmad a52dec libvorbis speex opencore-amr libdca faad2 libfdk-aac aom libwebp libavif libheif libmpeg2 libtheora libvpx libde265 libdv schroedinger dav1d rav1e svt-av1 gst-libav libvncserver python-beautifulsoup4 python-cssselect python-html5lib python-lxml-docs python-genshi libx11 lib32-libx11

# 14.(Optional) Install OpenMorrowind,ecwolf and gzdoom with sdlpop if you wish

 * sudo pacman -S openmw

 * yay -S ecwolf sdlpop

 * yay -S gzdoom

 * yay -S freedm

# 15. (Optional) Install spectacle for screenshots and neofetch to have "I use arch btw" in your CLI for screenshots and reddit,also man pages support to actually lear some stuff about what you install and flatpak for a wide variety of software,most of wich you can fing in AUR.

 * sudo pacman -S spectacle neofetch flatpak man

# 16. More dependencies:

 * sudo pacman -S sonnet

 * sudo pacman -S enchant

 * sudo pacman -Syyuu

# 17. Steam and lutris

 * sudo pacman -S steam lutris

# 18.(Optional) Gnome multi-writer tool, useful for creating usb sticks with OS installs and gnome-boxes for vm's useful GUI for QEMU:

 * sudo pacman -S gnome-multi-writer

 * sudo pacman -S gnome-boxes

# 19.(Optional) install itch for indies games and teamviewer:

 * yay -S itch teamviewer

# Enable teamviewerd.service when you see scary messages

 * sudo systemctl enable teamviewerd.service

 * sudo systemctl start teamviewerd.service

# 20.(Optional) Install Nvidia shadowplay for OBS Studio on Arch,so that gaming videos you make don't have tearing and look nice:

* # Get nvidia-patch: https://github.com/keylase/nvidia-patch
* Extract and go to the folder
* Open in terminal
* sudo ./patch-fbc.sh

# Get obs-nvfbc: https://gitlab.com/fzwoch/obs-nvfbc

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

# 21.More dependencies in case we missed them for audio and video multimedia decoding/encoding:

 * sudo pacman -S xvidcore libcurl-gnutls curl libcurl-compat

 * sudo pacman -S libcurl-gnutls curl libcurl-compat mingw-w64 gvfs-smb perl-getopt-argvfile gvfs-afc lib32-faudio lib32-expat lib32-at-spi2-atk lib32-fluidsynth lib32-glew glew lib32-glu lib32-gst-plugins-base-libs lib32-libjpeg-turbo lib32-mpg123 lib32-ocl-icd lib32-opus lib32-orc lib32-smpeg

 * sudo pacman -S lib32-wayland libaio x264 xvidcore lib32-libxvmc libxvmc ffms2 ffnvcodec-headers ffnvcodec-headers8.1 kmplayer opera-ffmpeg-codecs vivaldi-ffmpeg-codecs qtav avisynthplus vapoursynth mplayer

 * sudo pacman -S wavpack celt libmad a52dec libvorbis speex opencore-amr libdca faad2 libfdk-aac aom libwebp libavif libheif libmpeg2 libtheora libvpx libde265 libdv schroedinger dav1d rav1e svt-av1 gst-libav libdvdnav lib32-libxinerama vcdimager aubio python-aubio gtkmm libev libltc libuv xine-lib xine-ui 


 * sudo pacman -S gst-plugin-pipewire pipewire-jack libpipewire02 lib32-pipewire-jack lib32-pipewire pipewire-pulse pipewire-zeroconf

 * sudo pacman -S easyeffects zam-plugins mda.lv2 lv2-host vst-host vst3-host ladspa-host

 * sudo pacman -S kmplayer mpv
 
 * sudo pacman -S gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly
 
 * sudo pacman -S  cm256cc celt celt0.5.1 cppcodec speex libfdk-aac lib32-libdv lib32-flac lib32-celt kcodecs flac ffnvcodec-headers8.1 ffnvcodec-headers lib32-libtheora lib32-libvorbis lib32-libvpx lib32-libvpx1.3 lib32-opus lib32-speex libde265 libdv libfdk-aac libfreeaptx libilbc libjpeg-turbo mac mbelib  opera-ffmpeg-codecs opus-tools python-noseofyeti python-latexcodec python-reedsolo speex vivaldi-ffmpeg-codecs schroedinger libtheora


# 22.Ok we are done,update,clean the cache and orphan packages,so that your system does not get cluttered:

* sudo pacman -Syyuu

* sudo du -sh ~/.cache/

* sudo rm -rf ~/.cache/*

* sudo pacman -Scc

* yay -Scc

* su

* pacman -Qtdq | pacman -Rns -

* exit

# Check all the devices for errors/drivers as root:

* sudo lspci -v

# Check the system statius for errors:

* sudo dmesg

# Check history in the CLI terminal:

* history

* sudo history


# That's it you are good for using pure ArchLinux and don't forget to view archwiki for more advanced stuff:
https://wiki.archlinux.org/
and don't forget to install and use
* sudo pacman -S man
* man <your command here>
### Example: man pacman ##
Enjoy!
Thank you!
#gimalaji_blake
#Silent Gameplay
* My YouTube!
* https://www.youtube.com/c/SilentGameplays/
* Small video on how to manually install ArchLinux with UEFI:
* https://youtu.be/5mNEMWPVcec
* Enjoy!




