# OztekinGroup
Instructions to reinstall lab workstations

# OztekinGroup
Instructions to reinstall lab workstations

End Of Life: April 2027
## Creating Installer
1. Make sure the `Secure Boot` in UEFI BIOS is disabled.
2. Download [Ubuntu 22.04 LTS image](https://releases.ubuntu.com/jammy/ubuntu-22.04.2-desktop-amd64.iso).
3. Use [Ventoy](https://github.com/ventoy/Ventoy/releases) to create the installer media. You don't need to write images to the flash drive anymore.
4. Boot from the flash drive and select Ubuntu image.
5. Install Ubuntu. 
## Options
1. Choose `Minimal Installaion`. Select `Downloads updates`. Don't install third party software for now.
2. Installation Destination: Select `Something else`. Delete all existing partitions on the SSD. Set up 200 MB for `EFI System Partition`, **at least** 90 GB for `/`(use `ext4`) and the rest to `/home`(use `ext4`). No need to have `/swap` if the RAM is large enough.
3. set username as `oztekinlab` if it's a public workstation

## Initialization
Perform system update
```
sudo apt update
sudo apt upgrade
```
Go to `Disks` app and select HDD. Click `Edit mount options` and switch `Use Session Defaults` to `OFF`

Reboot

## Graphics Driver
### Nvidia
Go to `Additional Drivers` app, select the latest NVIDIA driver that has been **tested**.
Wait until finish then restart.
### AMD
`amdgpu` is built into kernel and AMD graphic cards are plug-and-play.
### Fully disable Wayland
Wayland is still enabled at login screen. Disable it to help remote desktop working properly.
```
sudo nano /etc/gdm3/custom.conf
```
Uncomment this line:
```
#WaylandEnable=false
```
then restart the service
```
sudo systemctl restart gdm3
```
## Install A****(you know...)
A**** works with Ubuntu 22.04 LTS.

Install prerequisites
```
sudo apt install -y libasound2 libatk-bridge2.0-0 libatk1.0-0 libatspi2.0-0 libavahi-client3 libavahi-common3 libblkid1 libbsd0 libc6 libcairo-gobject2 libcairo2 libcom-err2 libcups2 libdatrie1 libdbus-1-3 libdrm2 libepoxy0 libexpat1 libffi7 libfontconfig1 libfreetype6 libfribidi0 libgbm1 libgcc-s1 libgcrypt20 libgdk-pixbuf2.0-0 libglib2.0-0 libgmp10 libgnutls30 libgpg-error0 libgraphite2-3 libgssapi-krb5-2 libgtk-3-0  libharfbuzz0b libhogweed6 libice6 libidn2-0 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 liblz4-1 liblzma5 libmount1 libnettle8 libnspr4 libnss3 libp11-kit0 libpango-1.0-0 libpangocairo-1.0-0  libpangoft2-1.0-0 libpcre2-8-0 libpcre3 libpixman-1-0 libpng16-16 libselinux1 libsm6 libsqlite3-0  libsystemd0 libtasn1-6  libthai0 libunistring2 libuuid1 libwayland-client0 libwayland-cursor0 libwayland-egl1 libwayland-server0 libx11-6 libx11-xcb1 libxau6 libxcb-dri3-0 libxcb-render0 libxcb-shm0 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxdmcp6 libxext6 libxfixes3 libxft2 libxi6 libxinerama1 libxkbcommon0 libxrandr2 libxrender1 libxss1 libxtst6 lsb lsb-core libc6-i386 libxm4 xsltproc
```
Download A**** via **rsync** (DO NOT use SMB here)
```
sudo mkdir -p /share/Apps
sudo rsync -aP <username>@oztekingroup.dept.lehigh.edu:/mnt/Lab/IAC/A* /share/Apps
```

While waiting for it, you can continue and come back later.

Use `chown -R oztekinlab /share/Apps/A*` and `chgrp -R oztekinlab /share/Apps/A*` to fix permission.

## OpenFOAM
```
sudo apt install -y curl
curl https://dl.openfoam.com/add-debian-repo.sh | sudo bash
sudo apt install -y openfoam2212-default
echo 'source /usr/lib/openfoam/openfoam2212/etc/bashrc' >> ~/.bashrc 
```

## Paraview
Install Paraview 5.10 and its dependencies
```
sudo apt install -y paraview
```
## swak4foam
```
sudo apt install -y mercurial python2-dev python3-dev python-is-python3 bison lua5.3
cd $HOME
mkdir OpenFOAM
cd OpenFOAM
hg clone http://hg.code.sf.net/p/openfoam-extend/swak4Foam swak4Foam
cd swak4Foam
hg update fdfd52e35b46
export WM_NCOMPPROCS=$(nproc)
./Allwmake
```
If the compilation fails, run `./Allwmake` multiple rounds until it finishes.

## pyFoam
```
sudo apt install -y python3-pip gnuplot
sudo pip install pyfoam numpy
exit
```
Usage:
```
pyFoamPlotWatcher.py <logfilename>
```
## Install Anydesk
```
wget https://download.anydesk.com/linux/anydesk_6.2.1-1_amd64.deb
sudo apt install -y ./anydesk_6.2.1-1_amd64.deb
```
Then enable unattended access.

## Install Rustdesk
```
wget https://github.com/rustdesk/rustdesk/releases/download/1.1.9/rustdesk-1.1.9.deb
sudo apt install -y ./rustdesk-1.1.9.deb
```
The private server address is `oztekingroup.dept.lehigh.edu`

The public key(mandatory) is `Z3+VYaZLrFlV42+Rdl6LR8DKSuZ+Zgyx5BeK9Y22ksY=`

## Post-installation
1. Fix internal HDD ownership after reinstallation.
```
su root
chown -R <username> <path>
chgrp -R <groupname> <path>
```
