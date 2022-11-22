# OztekinGroup
Instructions to reinstall lab workstations

End Of Life: 2030
## Creating Installer
1. Make sure the `Secure Boot` in UEFI BIOS is disabled.
2. Download [Ubuntu 20.04 LTS image](https://releases.ubuntu.com/focal/ubuntu-20.04.5-desktop-amd64.iso)
3. Use [Ventoy](https://github.com/ventoy/Ventoy/releases) to install the image. You don't need to write images to the flash drive anymore.
4. Boot from the flash drive and select Ubuntu image.
5. Install Ubuntu. If you experience graphics issue, press `e` and append `nomodeset` then press ctrl+x to start installer.
## Options
1. Installation Destination: Choose the SSD and select `I will configure partitioning`. Delete all existing partitions and select `Standard Partition`. Set up 200 MB for `/boot/efi`, **at least** 80 GB for `/`(use `ext4`) and the rest to `/home`(use `ext4`). No need to have `/swap` if the RAM is large enough.
2. set username as `oztekinlab` if it's a public workstation

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

## Install A****(you know...)
A**** 22R2 works with Ubuntu 20.04 LTS.

Install prerequisites
```
sudo apt install -y libasound2 libatk-bridge2.0-0 libatk1.0-0 libatspi2.0-0 libavahi-client3 libavahi-common3 libblkid1 libbsd0 libc6 libcairo-gobject2 libcairo2 libcom-err2 libcups2 libdatrie1 libdbus-1-3 libdrm2 libepoxy0 libexpat1 libffi7 libfontconfig1 libfreetype6 libfribidi0 libgbm1 libgcc-s1 libgcrypt20 libgdk-pixbuf2.0-0 libglib2.0-0 libgmp10 libgnutls30 libgpg-error0 libgraphite2-3 libgssapi-krb5-2 libgtk-3-0  libharfbuzz0b libhogweed5 libice6 libidn2-0 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 liblz4-1 liblzma5 libmount1 libnettle7 libnspr4 libnss3 libp11-kit0 libpango-1.0-0 libpangocairo-1.0-0  libpangoft2-1.0-0 libpcre2-8-0 libpcre3 libpixman-1-0 libpng16-16 libselinux1 libsm6 libsqlite3-0  libsystemd0 libtasn1-6  libthai0 libunistring2 libuuid1 libwayland-client0 libwayland-cursor0 libwayland-egl1 libwayland-server0 libx11-6 libx11-xcb1 libxau6 libxcb-dri3-0 libxcb-render0 libxcb-shm0 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxdmcp6 libxext6 libxfixes3 libxft2 libxi6 libxinerama1 libxkbcommon0 libxrandr2 libxrender1 libxss1 libxtst6 lsb lsb-core libc6-i386 libxm4 xsltproc
```
Download A**** via **rsync** (DO NOT use SMB here)
```
sudo mkdir -p /share/Apps
sudo rsync -aP username@oztekingroup.dept.lehigh.edu:/mnt/Lab/gux215/A**** /share/Apps
```

While waiting for it, you can continue and come back later.

Use `chown -R oztekinlab A****` and `chgrp -R oztekinlab A****` to fix permission.

## OpenFOAM
```
sudo apt install -y curl
curl https://dl.openfoam.com/add-debian-repo.sh | sudo bash
sudo apt install -y openfoam2206-default
echo 'source /usr/lib/openfoam/openfoam2206/etc/bashrc' >> ~/.bashrc 
```

## Paraview
```
sudo apt install -y paraview
```

## swak4foam
```
sudo apt install mercurial python2-dev python3-dev python-is-python3
cd $HOME
mkdir OpenFOAM
cd OpenFOAM
hg clone http://hg.code.sf.net/p/openfoam-extend/swak4Foam swak4Foam
cd swak4Foam
hg update develop
export WM_NCOMPPROCS=$(nproc)
./AllwmakeAll
```
If the compilation fails, run `./AllwmakeAll` multiple rounds until it finishes.

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
## Install Anydesk & Teamviewer
```
wget https://download.anydesk.com/linux/anydesk_6.2.1-1_amd64.deb
wget https://download.teamviewer.com/download/linux/teamviewer_amd64.deb
sudo apt install anydesk_6.2.1-1_amd64.deb teamviewer_amd64.deb
```
Then enable unattended access.

## Post-installation
1. Fix internal HDD ownership after reinstallation.
```
su root
chown -R <username> <path>
chgrp -R <groupname> <path>
```
