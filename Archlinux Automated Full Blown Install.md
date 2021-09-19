 
# Full blown automated ArchLinux + KDE installation + dependencies and optional packages ###

# After booting into arch.iso launch

 * archinstall

# This script installation is pretty straightforward,use ext4 for main partition volume,add your main user to the sudoers file when asked,use systemd for boot (grub does not work with proprietary drivers when using archinstall script) for the sound driver select pipewire instead of pulseaudio and some optional packages firefox,libreoffice-fresh,chromium,vlc,git

# Configure the 32bit support in pacman.conf file

* sudo nano /etc/pacman.conf

# Uncomment these lines:

* [multilib]
* Include = /etc/pacman.d/mirrorlist

# Update pacman configuration

* sudo pacman -Syyuu

# Reinstall NVIDIA driver with proper libraries and dependencies for proper video support:

* sudo pacman -S nvidia nvidia-settings nvidia-utils lib32-nvidia-utils lib32-opencl-nvidia opencl-nvidia libvdpau libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader opencl-nvidia libvdpau vkd3d lib32-vkd3d lib32-libva-vdpau-driver

# Install a bunch of packages-dependencies:

* sudo pacman -S opencl-headers opencl-clhpp lib32-mesa-vdpau lib32-libva-mesa-driver

# Reboot for changes to take effect,use the X11 don't use Wayland under KDE or any other DE,especially for NVIDIA cards!:

* sudo reboot

# Back to the terminal to install a bunch of pacakges and dependencies:

* sudo pacman -S lib32-gnutls lib32-libldap lib32-libgpg-error lib32-libxml2 lib32-alsa-plugins lib32-sdl2 lib32-freetype2 lib32-dbus lib32-libgcrypt libgcrypt lib32-sdl lib32-sdl2 lib32-sdl_mixer lib32-sdl_ttf sdl2 sdl_gfx sdl2_image sdl2_mixer sdl2_net sdl2_ttf sdl_ttf mpg123 lib32-mpg123 lib32-libpulse lib32-jack

* sudo pacman -S lib32-fluidsynth fluidsynth lib32-gnutls lib32-libldap lib32-libgpg-error lib32-libxml2 lib32-sdl2 lib32-freetype2 lib32-dbus lib32-libgcrypt

* sudo pacman -S openal lib32-openal gtk3 gtk4 qt6  gvfs gvfs-afc gvfs-smb gvfs-gphoto2 gvfs-nfs clang freetds postgresql-libs unixodbc libfbclient mariadb-libs  gvfs-goa ark lrzip lzop p7zip unarchiver unrar gvfs-mtp neofetch spectacle go xdotool w3m libcaca jp2a chafa feh libheif djvulibre  imagemagick-doc libraw libxml2  ocl-icd openexr librsvg lirc aalib libgoom2  libnfs libkate projectm

# Optional installation of software in case you missed some:

* sudo pacman -S obs-studio vlc qbittorrent gnome-disk-utility gnome-multi-writer

# Another bunch of packages-dependencies:
* sudo pacman -S libdvdcss qt5-webkit lua52-socket libtiger ttf-dejavu zvbi libgme twolame libdc1394 libavc1394 libdvdcss lirc aalib libgoom2  libnfs libkate projectm exfat-utils dosfstools ntfs-3g  jfsutils flatpak lib32-opencl-nvidia opencl-nvidia libvdpau git xdg-utils gvfs-gphoto2 gphoto2 gvfs-nfs lib32-libiec61883 xvidcore lib32-libxvmc libxvmc ffmpeg ffmpegthumbs gst-libav xdg-desktop-portal-kde xdg-user-dirs

* sudo pacman -S lib32-libxxf86vm libxxf86vm gst-plugins-good gst-plugins-bad

* sudo pacman -S transcode libquicktime smpeg faac sndio

* sudo pacman -S sdl2_image sdl2_net sdl2_ttf sdl2_gfx sdl sdl_net sdl_mixer sdl_sound sdl_gfx sdl_ttf git networkmanager-qt nm-connection-editor

* sudo pacman -S libnma openresolv

* sudo pacman -S python-opengl qt5-webkit qt5-xmlpatterns qt5-websockets  qt5-remoteobjects qt5-serialport python-pyqt5 bash-completion

* sudo pacman -S  qt5-quick3d python-dbus python-gobject ufw gufw libnma libnm lib32-libnm libvoikko nuspell aspell lib32-giflib lib32-libxinerama  lib32-libxslt sane cups samba dosbox scummvm colord perl-term-readkey perl-tk  logrotate ipp-usb tk vtk ladspa-host vamp-host vamp-plugin-sdk libao sdl_image opencv rtaudio rubberband sox mono lua r tcl ocaml  gnome-desktop swh-plugins opencv t1utils dialog gcc-fortran tcl ttf-liberation

# Install wine and some more packages-dependencies:

* sudo pacman -S wine wine-mono wine-gecko

* sudo pacman -S lib32-v4l-utils lib32-libxcomposite lib32-opencl-icd-loader lib32-gst-plugins-base-libs

* sudo pacman -Syyuu

* sudo pacman -S opusfile

# ASP.NET/DOTNET libraries for Windows games and more C++ support,required by a bunch of API's:

* sudo pacman -S aspnet-runtime aspnet-runtime-3.1 dotnet-sdk dotnet-runtime-3.1 dotnet-sdk-3.1 dotnet-runtime gcc-libs grilo

* sudo pacman -S grilo-plugins

* sudo pacman -S lib32-vkd3d vkd3d

# Install yay using git,if you missed installing it see below:

* sudo pacman -S git

* git clone https://aur.archlinux.org/yay.git

* cd yay

* makepkg -si

# More packages-dependencies and dxvk(DirectX to Vulkan API for Windows games)

* sudo pacman -S mingw-w64 glslang

* yay -S dxvk

* sudo pacman -S alsa pavucontrol pipewire-alsa pipewire-jack alsa-utils alsa-card-profiles lib32-alsa-lib lib32-alsa-oss lib32-alsa-plugins alsa-plugins alsa-lib gdal wavpack celt libmad a52dec libvorbis speex opencore-amr libdca faad2 libfdk-aac aom libwebp libavif libheif libmpeg2 libtheora libvpx libde265 libdv schroedinger dav1d rav1e svt-av1 gst-libav libvncserver python-beautifulsoup4 python-cssselect python-html5lib python-lxml-docs python-genshi libx11 lib32-libx11

# Optional install OpenMorrowind,ecwolf and gzdoom with slpop if you wish

 * sudo pacman -S openmw

 * yay -S ecwolf sdlpop

 * yay -S gzdoom

 * yay -S freedm

# Optional install spectacle for screenshots and neofetch and man pages support and flatpak

 * sudo pacman -S spectacle neofetch flatpak man

# More dependencies:

 * sudo pacman -S sonnet

 * sudo pacman -S enchant

 * sudo pacman -Syyuu

# Steam and lutris

 * sudo pacman -S steam lutris

# Gnome multi-writer tool, useful for creating usb sticks with OS installs and gnome-boxes:

 * sudo pacman -S gnome-multi-writer

 * sudo pacman -S gnome-boxes

# Optional install itch and teamviewer:

 * yay -S itch teamviewer

# Enable teamviewerd.service when you see scary messages

 * sudo systemctl enable teamviewerd.service

 * sudo systemctl start teamviewerd.service

# (Optional) Install Nvidia shadowplay for OBS Studio on Arch:

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

# More dependencies in case we missed them audio and video:

 * sudo pacman -S xvidcore libcurl-gnutls curl libcurl-compat

 * sudo pacman -S libcurl-gnutls curl libcurl-compat mingw-w64 gvfs-smb perl-getopt-argvfile gvfs-afc lib32-faudio lib32-expat lib32-at-spi2-atk lib32-fluidsynth lib32-glew glew lib32-glu lib32-gst-plugins-base-libs lib32-libjpeg-turbo lib32-mpg123 lib32-ocl-icd lib32-opus lib32-orc lib32-smpeg

 * sudo pacman -S lib32-wayland libaio x264 xvidcore lib32-libxvmc libxvmc ffms2 ffnvcodec-headers ffnvcodec-headers8.1 kmplayer opera-ffmpeg-codecs vivaldi-ffmpeg-codecs qtav avisynthplus vapoursynth mplayer

 * sudo pacman -S wavpack celt libmad a52dec libvorbis speex opencore-amr libdca faad2 libfdk-aac aom libwebp libavif libheif libmpeg2 libtheora libvpx libde265 libdv schroedinger dav1d rav1e svt-av1 gst-libav xine libxinerama xine-lib libdbdnav lib32-libxinerama vcdimager aubio python-aubio gtkmm libev libltc libuv

 * sudo pacman -S gst-plugin-pipewire pipewire-jack libpipewire02 lib32-pipewire-jack lib32-pipewire pipewire-pulse pipewire-zeroconf

 * sudo pacman -S easyeffects zam-plugins mda.lv2 lv2-host vst-host vst3-host ladspa-host

 * sudo pacman -S kmplayer

# Ok we are done,update,clean the cache and orphan packages:

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
# example man pacman
Enjoy!
Thank you!
#gimalaji_blake
#Silent Gameplay
* My YouTube!
* https://www.youtube.com/c/SilentGameplays/
* Small video on how to install ArchLinux with UEFI:
* https://youtu.be/5mNEMWPVcec
* Enjoy!
