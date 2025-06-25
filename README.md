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

* ``sudo wipefs -a /dev/sda``

# For NVME SSD's

* ``sudo wipefs -a /dev/nvme0n1``

# (Opional) Deleting everything with shred command properly while overwriting so no forensics recovery is possible, also helps fix bad blocks in some cases:

# For regular SSD's/HDD's

* ``sudo shred -f -v /dev/sda``

# For NVME SSD's

* ``sudo shred -f -v /nvme0n1``

# Faster way to delete everything on SSD's/HDD's/NVME SSD's

* ``sudo blkdiscard /dev/sda``

* ``sudo blkdiscard /dev/nvme0n1``

# 3. Using cfdisk to format and create paritions on SSD/HDD before installing Arch Linux

# For regular SSD's/HDD's

* ``sudo cfdisk /dev/sda``

# For NVME SSD's

* ``sudo cfdisk /dev/nvme0n1``

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

* ``mkfs.fat -F32 /dev/sda``

# For NVME SSD's

* ``mkfs.fat -F32 /dev/nvme0n1p1``

# SWAP partition

# For regular SSD's/HDD's

* ``mkswap /dev/sda2``

* ``swapon /dev/sda2``

# For NVME SSD's

* ``mkswap /dev/nvme0n1p2``

* ``swapon /dev/nvme0n1p2``

# Primary partition

# For regular SSD's/HDD's

* ``mkfs.ext4 /dev/sda3``

# For NVME SSD's

* ``mkfs.ext4 /dev/nvme0n1p3``

# 6. Mounting the primary partition

# For regular SSD's/HDD's

* ``mount /dev/sda3 /mnt``

# For NVME SSD's

* ``mount /dev/nvme0n1p3 /mnt``

# Check if everything worked by using lsblk command.
* ``lsblk``

# 7.Base Arch Linux installation

# Check if internet works by using the ip link command.

# Standard Arch Linux installation with standard Ethernet and Wi-Fi support,nano as text editor:

* ``pacstrap /mnt base base-devel linux linux-headers linux-firmware nano networkmanager``

# Generate fstab file:

* ``genfstab -U /mnt >> /mnt/etc/fstab``

# (Optional choice) LTS(Long Term Support) Arch Linux installation with standard Ethernet and Wi-Fi support,nano as text editor:

* ``pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-firmware nano networkmanager``

# Generate fstab file:

* ``genfstab -U /mnt >> /mnt/etc/fstab``

# 8. Basic Arch Linux configuration with chroot

# Log into your installation as chroot:

* ``arch-chroot /mnt /bin/bash``

# Set locales:

* ``nano /etc/locale.gen``

# (Example) For US locale find this line en_US.UTF-8 in locale.gen file and uncomment the line.

# Generate locale

* ``locale-gen``

# Creating and adding a main user:

* ``useradd -g users -G power,storage,wheel -m user``

# Creating passwords for root and main user

# Password for root:

* ``passwd``

# Type in the root password.

# Password for main user:

* ``passwd user``

# Type in the user password.

# 9. Editing the sudoers file:

* ``visudo``

# or

* ``nano /etc/sudoers``

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

* ``sudo nano /etc/sudoers``

# 10. GRUB Bootloader Installation and configuration for UEFI boot

# Install the following packages along with GRUB Bootloader for UEFI support:

* ``pacman -S grub efibootmgr dosfstools os-prober mtools fuse3``

# Create an EFI boot directory on the EFI partition:

* ``mkdir /boot/efi``

# Mount the FAT32 EFI partition:

# For regular SSD's/HDD's

* ``mount /dev/sda1 /boot/efi``

# For NVME SSD's

* ``mount /dev/nvme0n1p1 /boot/efi``

# GRUB Bootloader Installation:

* ``grub-install --target=x86_64-efi --bootloader-id=grub --efi-directory=/boot/efi``

# Generating GRUB Bootloader configuration file:

* ``grub-mkconfig -o /boot/grub/grub.cfg``

# Additional measures for boot partition to not become unbootable

* ``mkdir /boot/efi/EFI/BOOT``

* ``cp /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/BOOT/BOOTX64.EFI``

# 11. Exit chroot,unmount Arch Linux ISO and reboot:

* ``exit``

* ``umount -R /mnt``

* ``reboot``

# 12. Desktop Environment installation,enabling the network and sound, Xorg installation,Date/Time configuration

# Ethernet

* ``sudo systemctl enable --now NetworkManager.service``

# Ethernet + WiFi

* ``sudo systemctl enable --now NetworkManager.service``

* ``sudo systemctl enable --now wpa_supplicant.service``

* ``sudo nmtui``

# Select the desired wireless connection,wired connections should be activated by default,connect to Wi-Fi using your password.

# Check if internet works:

* ``ping archlinux.org``

# 13. Set date,region and time.

* ``sudo timedatectl set-ntp true``

* ``sudo timedatectl list-timezones``

* ``sudo timedatectl set-timezone Zone/SubZone``

* ``sudo hwclock --systohc``

* ``sudo ln -sf /usr/share/zoneinfo/Region/City /etc/localtime``

# Hostname configuration

* ``sudo hostnamectl set-hostname myhostname``

# 14. Intel CPU firmware kernel support driver:

* ``sudo pacman -S intel-ucode``

# AMD CPU firmware kernel support driver:

* ``sudo pacman -S amd-ucode``

# 15. Xorg installation,Wayland is provided by default with all the popular DE's(Plasma\GNOME):

* ``sudo pacman -S xorg xorg-server xorg-xinit xorg-apps xterm xorg-xrandr xdg-user-dirs``

# Run to get user directories:

* ``xdg-user-dirs-update``

# 16. Enable 32-bit support for Steam,Wine,Lutris

* ``sudo nano /etc/pacman.conf``

# Uncomment these lines:

* [multilib] Include = /etc/pacman.d/mirrorlist

# Update:

* ``sudo pacman -Syu``

# 17. Install audio support

* ``sudo pacman -S alsa-firmware alsa-utils pipewire pipewire-alsa pipewire-pulse``

# (Older) Install audio alsa/pulseaudio drivers and utilities:

* ``sudo pacman -S alsa-firmware alsa-utils pulseaudio pulseaudio-alsa ``


# 18. GNOME/KDE Plasma/XFCE/Desktop environments

# XFCE

* ``sudo pacman -S xfce4 xfce4-goodies gvfs``

# Display Managers:

* ``sudo pacman -S sddm sddm-kcm``
  
* ``sudo systemctl enable --now sddm``

# Or

* ``sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings``
  
* ``sudo systemctl enable --now lightdm.service``

# 19. GNOME

* ``sudo pacman -S gnome gnome-extra``

# For missing backends on GNOME:

* ``sudo pacman -S gnome-packagekit``

# Display Manager:

* ``sudo pacman -S gdm libgdm``

* ``sudo systemctl enable --now gdm``

# 20. KDE PLasma, sddm display manager is packaged with Plasma package used by default:

* ``sudo pacman -S plasma kde-applications``

# For missing backends on KDE Plasma:

* ``sudo pacman -S packagekit-qt6``

# NB! X11 support for KDE Plasma is now only available when plasma-x11-session package is installed:

* ``sudo pacman -S plasma-x11-session``

# Inconsistent brightness levels fix Plasma 6.3 and higher:

* ``systemctl --user edit plasma-powerdevil.service``

# Add these lines, save Ctrl+O Ctrl+X and reboot:

* ``[Service]``
* ``Environment=POWERDEVIL_NO_DDCUTIL=1``

# Display Manager:

* ``sudo pacman -S sddm sddm-kcm``

* ``sudo systemctl enable --now sddm``

# Extra Deepin

* ``sudo pacman -S lightdm  lightdm-gtk-greeter lightdm-gtk-greeter-settings``

* ``sudo pacman -S deepin deepin-extra``

* ``sudo nano /etc/lightdm/lightdm.conf``

# Add this line then Ctrl+O,Ctrl+X

* greeter-session=lightdm-deepin-greeter
  
* ``sudo systemctl enable --now lightdm ``

# Extra Pantheon

* ``sudo pacman -S lightdm lightdm-pantheon-greeter lightdm-gtk-greeter lightdm-gtk-greeter-settings``

* ``sudo pacman -S pantheon``

* ``sudo systemctl enable --now lightdm ``

# 21.(Optional) Additional dependencies

# (Optional) for noveau drivers 

* ``xf86-video-vesa mesa``

# 22. Enable the GUI desktop to start at launch via the required display manager for yur desktop environment: 

# SDDM with customizable settings:

* ``sudo pacman -S sddm sddm-kcm``
  
* ``sudo systemctl enable --now sddm``

# LIGHTDM with customizable settings:

* ``sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings``
  
* ``sudo systemctl enable --now lightdm``

# GDM with customizable settings:

* ``sudo pacman -S gdm libgdm``
  
* ``sudo systemctl enable --now gdm``

# (Longer way) In case LIGHTDM is missing: 

* ``sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings``
* ``sudo systemctl enable lightdm.service``
* ``sudo systemctl start lightdm.service``

# In case GDM is missing:

* ``sudo pacman -S gdm libgdm``
* ``sudo systemctl enable gdm.service``
* ``sudo systemctl start  gdm.service``

# In case SDDM is missing:

* ``sudo pacman -S sddm sddm-kcm``
* ``sudo systemctl enable sddm``
* ``sudo systemctl start sddm``

# (Optional) Use this theme for better icons on all DE's:

* ``sudo pacman -S papirus-icon-theme``
* ``sudo pacman -S noto-fonts noto-fonts-emoji``
* ``sudo pacman -S arc-gtk-theme``

# 23. Install NVIDIA protprietary or AMD open source drivers and utilities:

# For Nvidia Non-LTS (rolling)
* ``sudo pacman -S nvidia nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers``
  
# Run mkinitcpio after installation and reboot:

* ``sudo mkinitcpio -P``
  
# For Nvidia LTS(Long Term Support)
* ``sudo pacman -S nvidia-lts nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers``

# Run mkinitcpio after installation and reboot:
* ``sudo mkinitcpio -P``

# NVIDIA DKMS Driver For Zen and Multiple Kernels

* ``sudo pacman -S nvidia-dkms nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers``

# Run mkinitcpio after installation and reboot:

* ``sudo mkinitcpio -P`` 

# NVIDIA Open Source Driver
* ``sudo pacman -S nvidia-open nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers`` 

# Run mkinitcpio after installation and reboot:

* ``sudo mkinitcpio -P``

# NVIDIA Open Source DKMS Driver

* ``sudo pacman -S nvidia-open-dkms nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau lib32-libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d opencl-headers opencl-clhpp vulkan-validation-layers lib32-vulkan-validation-layers`` 

# Run mkinitcpio after installation and reboot:

* ``sudo mkinitcpio -P`` 

# NVIDIA DRM Wayland Support and generally improved gaming support on X11 as well With RTX Cards:

# Edit /etc/mkinitcpio.conf

* ``sudo nano /etc/mkinitcpio.conf``
  
# * Change MODULES=()
# * To MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)

* ``sudo mkinitcpio -P``
  
# Edit GRUB /etc/default/grub for other bootloaders check the Arch Wiki https://wiki.archlinux.org/title/Kernel_module#Setting_module_options

* ``sudo nano /etc/default/grub``
  
# * Change GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet"
# * To GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet nvidia_drm.modeset=1"

* ``sudo grub-mkconfig -o /boot/grub/grub.cfg``
* ``sudo reboot``

# to check if everything works:

* ``sudo cat /sys/module/nvidia_drm/parameters/modeset``

# Extra Steps with GNOME/GDM

# Removing the gdm udev that disables wayland
* ``sudo ln -s /dev/null /etc/udev/rules.d/61-gdm.rules``
* ``sudo reboot``

# Dependencies,usually provided by KDE/GNOME

* ``sudo pacman -S egl-wayland libglvnd``
  
# After NVIDIA Driver updates run:

* ``sudo mkinitcpio -P``

# Reboot for changes to take effect:

* ``sudo reboot``

# Run this to improve perdormance:

* ``sudo nvidia-xconfig``

# 24. Drivers For AMD GPU's, no need to tinker with DRM kernel parameters:

* ``sudo pacman -S mesa mesa-utils lib32-mesa opencl-mesa lib32-opencl-mesa lib32-vulkan-radeon vulkan-radeon vulkan-mesa-layers lib32-vulkan-mesa-layers glu lib32-glu vulkan-icd-loader lib32-vulkan-icd-loader vkd3d lib32-vkd3d xf86-video-amdgpu``
* ``sudo mkinitcpio -P``
* ``reboot``

# AMD Vulkan Drivers with proprietary shader stack if vulkan-radeon lib32-vulkan-radeon give issues,can be installed with everything else without conflicts.

* ``sudo pacman -S amdvlk lib32-amdvlk``

# 25. Additional optimizations for gaming:

# Increase max vm.max_map_count to prevent more demanding games from crashing:

* ``sudo nano /usr/lib/sysctl.d/10-arch.conf``
* change vm.max_map_count=2147483642
* ``sudo reboot``
* ``cat /proc/sys/vm/max_map_count``

# gamemode and gamescope:
* ``sudo pacman -S gamemode lib32-gamemode``
* ``sudo pacman -S gamescope``

# mangohud for FPS measurements and monitoring:

* ``sudo pacman -S mangohud lib32-mangohud``

# 26. Monitoring tools for temps\usage

# For monitoring all GPU's:

* ``sudo pacman -S nvtop``

# Whole System btop or htop:
* ``sudo pacman -S btop rocm-smi-lib``
* ``sudo pacman -S htop``

# Only temps
* ``sensors``
  
# 27. Installing AUR helper yay
* ``sudo pacman -S git``

# NB No sudo!

* ``git clone https://aur.archlinux.org/yay.git``
* ``cd yay``
* ``makepkg -si``


# 28. Removing packages, clearing cache,orphans,unneeded dependencies so the system does not get cluttered:

* ``sudo pacman -Scc``

* ``sudo pacman -Sc``

* ``sudo du -sh ~/.cache/``

* ``sudo rm -rf ~/.cache/*``

* ``sudo pacman -Qtdq``

* ``sudo pacman -Rns $(pacman -Qtdq)``

# For yay AUR helper:

* ``yay -Sc``

* ``yay -Scc``
 
# Cleaning pacman cache

* ``sudo rm /var/cache/pacman/pkg/*``

# Removing only the stated packages without dependencies:

* ``sudo pacman -Rdd package name``

# Removing only the stated packages with dependencies

* ``sudo pacman -Rscn application name``

# Recursively removing orphans when cluttered 

* ``su``
* ``pacman -Qtdq | pacman -Rns -``
* ``exit``

# 29.(Optional)Installing other stuff:

# Archiver tool for Plasma:
* ``sudo pacman -S ark lrzip lzop 7zip unarchiver unrar`` 

# Archiver tools for GNOME,for XFCE both ark and file-roller can be used:
* ``sudo pacman -S file-roller nemo-fileroller unzip lzop 7zip unrar unarchiver``

# Media players (pick one or two or all or what works best):

* ``sudo pacman -S  mpv vlc dragon kmplayer celluloid mpd qtav showtime``

# Browsers 

* ``sudo pacman -S firefox-developer-edition``

* ``sudo pacman -S falkon``
  
* ``sudo pacman -S epiphany``

* ``sudo pacman -S chromium``

* ``sudo pacman -S firefox``

* ``yay -S firefox-esr-bin``

* ``yay -S librewolf-bin``

* ``yay -S google-chrome``

* ``yay -S microsoft-edge-stable-bin``

# LibreOffice rolling:

* ``sudo pacman -S libreoffice-fresh``

# OR stable:

* ``sudo pacman -S libreoffice-still``

# 30. Other stuff,inlcuding OBS Studio.

* ``sudo pacman -S obs-studio``

* ``sudo pacman -S flatpak``

* ``sudo pacman -S openra``

* ``sudo pacman -S gimp``

* ``sudo pacman -S krita``

* ``sudo pacamn -S kate``

# Torrent clients:

* ``sudo pacman -S qbittorrent``
* ``sudo pacman -S ktorrent``
* ``sudo pacman -S transmission-qt``

# OR for GTK based DE's (XFCE/GNOME)

* ``sudo pacman -S transmission-gtk``

# Video Editors:

* ``sudo pacman -S kdenlive``
* ``sudo pacman -S shotcut``
* ``sudo pacman -S blender``
* ``sudo pacman -S openshot``

# Emulators:
* ``sudo pacman -S libretro``
* ``sudo pacman -S dosbox``
* ``sudo pacman -S scummvm``
* ``sudo pacman -S pscx2``
* ``sudo pacman -S retroarch``

# Teamviewer install and configure from AUR:

* ``yay -S teamviewer``
* ``sudo systemctl enable --now teamviewerd.service``

# More mods,games and tools:

* ``sudo pacman -S openmw``

* ``sudo pacman -S supertux``

* ``sudo pacman -S freedroidrpg``

* ``sudo pacman -S supertuxkart``

* ``sudo pacman -S kapman``

* ``sudo pacman -S 0ad``

* ``sudo pacman -S xonotic``

* ``sudo pacman -S wesnoth``

* ``sudo pacman -S openra``

* ``sudo pacman -S freeciv``

* ``sudo pacman -S dwarffortress``

* ``sudo pacman -S bass``

* ``flatpak install flathub com.moddb.TotalChaos``

* ``yay -S dunelegacy``

* ``yay -S sdlpop``

* ``yay -S opensurge``

* ``yay -S commander-genius-git``

# Partition managers:

* ``sudo pacman -S gnome-disk-utility``
* ``sudo pacman -S partitionmanager``
* ``sudo pacman -S gparted``
  
# GPU Screen Recorder similar to shadowplay:

* ``yay -S gpu-screen-recorder``
* ``yay -S gpu-screen-recorder-gtk``

# (Optional)Installing VKD3D and/or DXVK for DX10/DX11 conversion support outswide of Steam/Lutris:

* ``sudo pacman -S python-protobuf lib32-vkd3d vkd3d``
* ``yay -S dxvk-bin``

# 31. Install a bunch of dependencies/packages to make life sort of easier(Optional):

* ``sudo pacman -S gtk3 gtk4 qt6 gvfs ``

# 32. Dependencies multimedia libraries for decoding/encoding (OPTIONAL if you want full no bloatware system skip these,or choose the ones you need):

* ``sudo pacman -S fluidsynth lib32-fluidsynth openal lib32-openal gvfs gvfs-nfs libkate gst-plugins-base gst-plugins-bad-libs gst-libav lib32-gst-plugins-good gst-plugin-gtk lib32-gstreamer lib32-gst-plugins-base lib32-gst-plugins-base-libs xvidcore lib32-libxvmc libxvmc ffmpeg gst-libav gst-plugins-good gst-plugins-bad smpeg faac sndio libnma openresolv x264 x265 opus sane lame libao wavpack libmad a52dec libvorbis faad2 libmpeg2 libtheora libvpx libde265 libdv schroedinger dav1d rav1e gst-libav gst-plugins-base gst-plugin-va gst-plugins-good gst-plugins-bad gst-plugins-ugly gst-plugin-pipewire lib32-pipewire pipewire-zeroconf flac lib32-flac smpeg lib32-smpeg mac opus lib32-opus opus-tools opusfile libmpeg2 libavif faac libwebp libheif libjxl jasper aom svt-av1``

# (In case you are using meson builds)

* ``sudo pacman -S meson``

# Install wine and some more packages-dependencies for gaming:

* ``sudo pacman -S wine wine-mono wine-gecko lutris steam``
* ``sudo pacman -S mono``  
* ``sudo pacman -S lib32-v4l-utils lib32-libxcomposite lib32-opencl-icd-loader lib32-gst-plugins-base-libs grilo grilo-plugins``
* ``sudo pacman -Syu``
* ``sudo pacman -S opusfile``

# For better wine support

* ``sudo systemctl restart systemd-binfmt``

# (Optional) More packages-dependencies for Wine,Dosbox,Scummvm for older games:

* ``sudo pacman -S mingw-w64 glslang lib32-libvorbis ``

# (Optional) Install Glourious Eggroll Proton GE the easy way:
 * Download the latest release here: https://github.com/GloriousEggroll/proton-ge-custom/releases
 * Extract,enable hidden files and folders 
 * Create a folder in your /home/user/steam/root/compatibilitytools.d if it does not exist.
 * Copy/paste the extracted GE folder into /home/user/config/.steam/root/compatibilitytools.d
 * Restart Steam,enjoy the custom GE build
 
# (Optional) Install spectacle for screenshots (KDE PLASMA) and fastfetch to have "I use arch btw" in your CLI for screenshots and reddit,also man pages support to actually learn some stuff about what packages you install and flatpak for a wide variety of software,most of which you can find in AUR.

 * ``sudo pacman -S spectacle fastfetch man``
 
 * ``sudo pacman -S sonnet``

 * ``sudo pacman -S enchant``

 * ``sudo pacman -Syu``

# (Optional) Gnome boxes, useful for creating usb sticks with OS installs and gnome-boxes for vm's useful GUI for QEMU:

 * ``sudo pacman -S gnome-boxes``
 * ``sudo pacman -S virt-manager qemu-desktop``
 
 # To use virt manager isntead of GNOME Boxes properly run: 
 
 * ``sudo systemctl enable libvirtd.service``
 * ``sudo systemctl start libvirtd.service``
 
 # OR 
 
 * ``sudo systemctl enable --now libvirtd.service``
 
# Check this website for additional driver/video support on vm's for both GNOME BOXES and virtmanager:
* https://www.spice-space.org/download.html


# 34. Update the whole system using:

* ``sudo pacman -Syu``

# OR

* ``sudo pacman -Syyu``
* ``sudo pacman -Syyu --refresh -y``

# Check the history of CLI:

* ``history``

# 35.Clear terminal by using:

* ``clear``

# 36.Auto-mounting drives the easy way:

* ``sudo pacman -S gnome-disk-utility``

# Automount using gnome disk utility:

* Edit mount options
* Add this line: 
* nosuid,nodev,nofail,x-gvfs-show,auto
  
# show system status
* ``sudo systemctl status``

# refresh system databases in case something breaks
* ``sudo pacman -Syyu``

# full system update
* ``sudo pacman -Syu``


# Audio enhancements similar to Windows Loudness Equalization for games like Witcher 3
# Pipewire:

* Set in Sound Sonfiguration as Pro Audio instead of Analog Stereo Duplex
  
* ``sudo nano /usr/share/pipewire/pipewire.conf``

# Find this line 

* ``#default.clock.allowed-rates = [ 48000 ]``

# Change to

* ``#default.clock.allowed-rates = [ 44100 48000 96000 ]``

# Install required dependencies for Easy Effects:
* ``sudo pacman -Syu easyeffects lsp-plugins calf zam-plugins-lv2``

# Launch EasyEffects and apply presets either from this repository or download the one provided here LoudnessEqualizer.json:

* https://github.com/Digitalone1/EasyEffects-Presets
  
# 35. Creating a bootable Windows 10 USB using Disks utility (Possible on any linux distro even without GNOME)
* Download a Windows image from MS link below:
* https://www.microsoft.com/en-us/software-download/windows10
* Insert USB Drive
* Launch Disks Utility
* Select your USB Drive and in the top right=corner click the menu select Format Disk
* In Partitioning select Compatible with modern systems and hard disks>2TB (GPT)
* Click Format wait for it to finish 
* Click Partition> For Use with Windows(NTFS) (in Volume label type Windows or ESD)
* Mount the USB and Open it
* Go to the place where you downloaded Windows 10 ISO and select Open with Disk Image Mounter
* Open Copy everything from the Windows 10 ISO and paste into your USB Drive,wait for it to finish(takes a while)

# (Optional) Custom DNS configuration

* ``sudo nano /etc/resolv.conf``
# change/add lines from

OpenDNS

* nameserver 208.67.222.222
* nameserver 208.67.220.220
* nameserver 2620:119:35::35	
* nameservet 2620:119:53::53

Google DNS

* nameserver 8.8.8.8
* nameserver 8.8.4.4

CloudFlare DNS

* nameserver 1.1.1.1
* nameserver 1.0.0.1
* nameserver 2606:4700:4700::1111
* nameserver 2606:4700:4700::1001

Alternate DNS	

* nameserver 198.101.242.72
* nameserver 23.253.163.53

Dyn DNS

* nameserver 216.146.35.35
* nameserver 216.146.36.36

OpenNIC DNS
* nameserver 58.6.115.42
* nameserver 58.6.115.43
* nameserver 119.31.230.42
* nameserver 2001:470:8388:2:20e:2eff:fe63:d4a9
* nameserver 2001:470:1f07:38b::1
* nameserver 2001:470:1f10:c6::2001
* nameserver 200.252.98.162
* nameserver 217.79.186.148
* nameserver 81.89.98.6
* nameserver 78.159.101.37
* nameserver 203.167.220.153
* nameserver 82.229.244.191
* nameserver 216.87.84.211
* nameserver 66.244.95.20
* nameserver 207.192.69.155
* nameserver 72.14.189.120

# NB! Optional (not recommended) disabling kernel and driver updates for more stable experience
* ``sudo nano /etc/pacman.conf``
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

* ``lspci -v``
* ``sudo lspci -v``

# (Optional) mkinitcpio:

* ``sudo mkinitcpio``

# That's it you are good for using pure ArchLinux and don't forget to view archwiki for more advanced commands and packages:
https://wiki.archlinux.org/

# Don't forget to install and use man
* ``sudo pacman -S man``
* ``man`` (your command here)

# Example: man pacman

# Fixing the new systemd suser@XXX.service is not active, cannot reload. errors for sddm, gdm, lightdm display managers, insert the one you use.

* ``sudo chage -l sddm``

# Or 

* ``sudo chage --list sddm``

# Then Run

* ``sudo chage -E -1 sddm``

* ``sudo reboot``

# If you start getting errors like  "signature from "John Smith <john.smith@archlinux.org>" is marginal trust" do these steps:
* ``sudo pacman -S archlinux-keyring``

* ``sudo pacman -Syu  archlinux-keyring``

* ``sudo pacman -Syyuu``

# Run  this in case it still occurs:
* ``sudo pacman-key --refresh-keys``

* ``sudo pacman -Syu archlinux-keyring``

# Extra steps workarounds:
# Enable pipewire without restart/configure alsa settings for audio devices, requires alsa-firmware and alsa-utils packages:

* ``systemctl --user restart pipewire pipewire-pulse``
* ``alsamixer``

# Refresh mirrorlists:

* ``sudo pacman -Syyu --refresh -y``

# Install advanced networking tools:
* ``sudo pacman -S bind``


# Remove leftovers from apps and other files

* ``find ~ -type d -name 'app-name*'``

* ``rm -r /home/user/.local/share/application/app/x86_64/app-name``

* ``find ~ -type f -name '*.torrent *'``

# (Optional/Experimental) Secure boot setup cheat sheet:

* ``sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --modules="tpm" --disable-shim-lock``

1. Regenerate your grub configuration:

* ``sudo grub-mkconfig -o /boot/grub/grub.cfg``

2. Install the sbctl tool:

* ``sudo pacman -S sbctl``

2. As a pre-requisite, in your UEFI settings, set your secure boot mode to setup mode. Upon re-booting, verify that you are in setup mode:

* ``sbctl status``

4. Create your custom secure boot keys:

* ``sudo sbctl create-keys``

5. Enroll your custom keys (note -m is required to include Microsoft's CA certificates)

* ``sudo sbctl enroll-keys -m``

6. Verify that your keys have successfully been enrolled:

* ``sbctl status``

7. Check which files need to be signed for secure boot to work:

* ``sudo sbctl verify``

8. Sign all unsigned files (adjust to your setup):

* ``sudo sbctl sign -s /efi/EFI/GRUB/grubx64.efi``

9. You may get an error because of an issue with certain files being immutable. To make those files mutable, run the following command for each file then re-sign afterwards:

* ``sudo chattr -i /sys/firmware/efi/efivars/<filename>``

10. Verify that everything has been signed:

* ``sudo sbctl verify``

11. In your UEFI settings, enable secure boot, and reboot. Verify that secure boot is enabled:

* ``sbctl status``

# NB sbctl comes with a pacman hook for automatic signing, so you don't need to worry when you update your system. 

# Save the changes and exit,reboot,you are good 

# silentgameplays Youtube channel:
* https://www.youtube.com/@silentgameplays/

* Enjoy!
Thank you!
# silentgameplays



