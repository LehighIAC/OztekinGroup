# OztekinGroup
Instructions to reinstall lab workstations

End Of Life: April 2027
## Creating Installer
1. Make sure the `Secure Boot` in UEFI BIOS is disabled.
2. Download [Ubuntu 22.04 LTS image](https://releases.ubuntu.com/jammy/ubuntu-22.04.4-desktop-amd64.iso).
3. Use [Ventoy](https://github.com/ventoy/Ventoy/releases) to create the installer media. You don't need to write images to the flash drive anymore.
4. Boot from the flash drive and select Ubuntu image.
5. Install Ubuntu. 
## Options
1. Choose `Minimal Installaion`. Select `Downloads updates`. Don't install third party software for now.
2. Installation Destination: Select `Something else`. Delete all existing partitions on the SSD. Set up 200 MB for `EFI System Partition`, **at least** 100 GB for `/`(use `ext4`) and the rest to `/home`(use `ext4`). No need to have `/swap` if the RAM is large enough.
3. set username as `oztekinlab` if it's a public workstation

## Initialization
Perform system update
```
sudo apt update
sudo apt upgrade
```
Go to `Disks` app and select HDD. Click `Edit mount options` and switch `Use Session Defaults` to `OFF`

Reboot
## Get rid of Snap
**It is recommended to copy this section to** `gedit` **as you will lose** `firefox` **during the process**.

Run the following command for **three times**
```
for p in $(snap list | awk '{print $1}'); do
  sudo snap remove $p
done
```
until it prints:
```
No snaps are installed yet. Try 'snap install hello-world'.
```
Remove snap files:
```
sudo systemctl stop snapd
sudo systemctl disable --now snapd.socket

for m in /snap/core/*; do
   sudo umount $m
done

sudo apt autoremove --purge snapd

rm -rf ~/snap
sudo rm -rf /snap
sudo rm -rf /var/snap
sudo rm -rf /var/lib/snapd
sudo rm -rf /var/cache/snapd
```
Configure `apt`
```
sudo sh -c "cat > /etc/apt/preferences.d/no-snapd.pref" << EOL
Package: snapd
Pin: release a=*
Pin-Priority: -10
EOL
```
```
sudo sh -c "cat > /etc/apt/preferences.d/no-firefox.pref" << EOL
Package: firefox
Pin: release a=*
Pin-Priority: -10
EOL
```
Install clean version software
```
sudo add-apt-repository ppa:mozillateam/ppa
sudo sh -c "cat > /etc/apt/preferences.d/mozillateam-firefox.pref" << EOL
Package: firefox*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 501
EOL
sudo apt update
sudo apt install firefox-esr gnome-software
```
## Graphics Driver
### Nvidia
Go to `Additional Drivers` app, select the latest NVIDIA driver that has been **tested**.
### AMD
`amdgpu` is built into kernel and AMD graphic cards are plug-and-play.
### Fully disable Wayland
Wayland is still enabled at login screen. Disable it to help remote desktop working properly.
```
sudo sed -i 's/#WaylandEnable=false/WaylandEnable=false/' /etc/gdm3/custom.conf
```
then reboot.
## Install A****(you know...)
A**** works with Ubuntu 22.04 LTS.

Install prerequisites
```
sudo apt install -y libasound2 libatk-bridge2.0-0 libatk1.0-0 libatspi2.0-0 libavahi-client3 libavahi-common3 libblkid1 libbsd0 libc6 libcairo-gobject2 libcairo2 libcom-err2 libcups2 libdatrie1 libdbus-1-3 libdrm2 libepoxy0 libexpat1 libffi7 libfontconfig1 libfreetype6 libfribidi0 libgbm1 libgcc-s1 libgcrypt20 libgdk-pixbuf2.0-0 libglib2.0-0 libgmp10 libgnutls30 libgpg-error0 libgraphite2-3 libgssapi-krb5-2 libgtk-3-0  libharfbuzz0b libhogweed6 libice6 libidn2-0 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 liblz4-1 liblzma5 libmount1 libnettle8 libnspr4 libnss3 libp11-kit0 libpango-1.0-0 libpangocairo-1.0-0  libpangoft2-1.0-0 libpcre2-8-0 libpcre3 libpixman-1-0 libpng16-16 libselinux1 libsm6 libsqlite3-0  libsystemd0 libtasn1-6  libthai0 libunistring2 libuuid1 libwayland-client0 libwayland-cursor0 libwayland-egl1 libwayland-server0 libx11-6 libx11-xcb1 libxau6 libxcb-dri3-0 libxcb-render0 libxcb-shm0 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxdmcp6 libxext6 libxfixes3 libxft2 libxi6 libxinerama1 libxkbcommon0 libxrandr2 libxrender1 libxss1 libxtst6 lsb lsb-core libc6-i386 libxm4 xsltproc
```
Download A**** via **rsync** (DO NOT use SMB here)
```
sudo mkdir -p /share/Apps
sudo rsync -aP <your_user_name>@oztekingroup.dept.lehigh.edu:/mnt/Lab/IAC/A* /share/Apps
```

While waiting for it, you can continue to the next section and come back later.

Copy desktop shortcut, then right click and select `Allow Launching`

```cp /share/Apps/A*.desktop ~/Desktop```

If you can't open it, run `chown -R oztekinlab /share/Apps/A*` and `chgrp -R oztekinlab /share/Apps/A*` to fix permission.

## OpenFOAM
```
sudo apt install -y curl
curl https://dl.openfoam.com/add-debian-repo.sh | sudo bash
sudo apt install -y openfoam2306-default
echo 'source /usr/lib/openfoam/openfoam2306/etc/bashrc' >> ~/.bashrc 
```

## Install Paraview
Install Paraview 5.10 and its dependencies
```
sudo apt install -y paraview
```
## Compile swak4foam
```
sudo apt install -y mercurial python2-dev python3-dev python-is-python3 bison lua5.3
cd $HOME
mkdir OpenFOAM
cd OpenFOAM
hg clone http://hg.code.sf.net/p/openfoam-extend/swak4Foam swak4Foam
cd swak4Foam
hg update develop
ln -s swakConfiguration.automatic swakConfiguration
export WM_NCOMPPROCS=$(nproc)
./Allwmake
```
If the compilation fails, run `./Allwmake` multiple rounds until it compiles without error.

## Install pyFoam
```
sudo apt install -y python3-pip gnuplot
sudo pip install pyfoam numpy
```
Usage:
```
pyFoamPlotWatcher.py <logfilename>
```
## Install Anydesk
```
wget https://download.anydesk.com/linux/anydesk_6.3.2-1_amd64.deb
sudo apt install -y ./anydesk_6.3.2-1_amd64.deb
rm ./anydesk_6.3.2-1_amd64.deb
```
Then set the password to enable unattended access.

## Install Rustdesk (in case Anydesk says you are a commercial user)
```
wget https://github.com/rustdesk/rustdesk/releases/download/1.2.3-2/rustdesk-1.2.3-2-x86_64.deb
sudo apt install -y ./rustdesk-1.2.3-2-x86_64.deb
rm ./rustdesk-1.2.3-2-x86_64.deb
```
The private server address is `oztekingroup.dept.lehigh.edu`

The public key(mandatory) is `Z3+VYaZLrFlV42+Rdl6LR8DKSuZ+Zgyx5BeK9Y22ksY=`

## Post-installation
1. In `Disks` select `Take ownership` to fix file ownership.
![Screenshot from 2024-02-27 12-35-35](https://github.com/BrushXue/OztekinGroup/assets/12702149/14ed4d6a-f1a4-4f32-a536-8510daec4d94)

