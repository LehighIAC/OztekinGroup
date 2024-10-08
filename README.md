# OztekinGroup
Instructions to reinstall lab workstations

End Of Life: 2027
## Installer
It is recommended to install the OS without Internet.
1. Make sure the `Secure Boot` in UEFI BIOS is disabled.
2. Download [Linux Mint 21 image](https://linuxmint.com/edition.php?id=311).
3. Use [Ventoy](https://github.com/ventoy/Ventoy/releases) to create the installer media. You don't need to write images to the flash drive anymore.
4. Boot from the flash drive and select Linux Mint image, then select `Boot in normal mode`. In the desktop environment, select `Install Linux Mint`. 
5. Installation type: Select `Something else`. Format all existing partitions on the SSD(check device name). Set up 200 MB for `EFI System Partition`, 102400 MB for `/`(use `ext4`) and the rest to `/home`(use `ext4`). (Make sure select the appropriate free space). No need to have `/swap` since we have 128GB RAM.
6. Set username to whatever your want for your workstation, or `oztekinlab` for lab shared workstations.

## Initialization
Perform system update
```
sudo apt update
sudo apt upgrade
```
Go to `Disks` app and select HDD. Click `Edit mount options` and switch `Use Session Defaults` to `OFF`. Select `Take ownership` (select recursive) to fix file ownership.

Disable printer discovery:
```
sudo apt purge cups-browsed
```
Then **reboot**

## Graphics Driver
### Nvidia
Go to `Driver Manager` app, select the latest NVIDIA driver that has been **tested**. Then **reboot**.
### AMD
`amdgpu` opensource driver is built into kernel and AMD graphic cards are plug-and-play.

## Install A****(you know...)
Open a terminal
Install prerequisites
```
sudo apt install -y libasound2 libatk-bridge2.0-0 libatk1.0-0 libatspi2.0-0 libavahi-client3 libavahi-common3 libblkid1 libbsd0 libc6 libcairo-gobject2 libcairo2 libcom-err2 libcups2 libdatrie1 libdbus-1-3 libdrm2 libepoxy0 libexpat1 libffi7 libfontconfig1 libfreetype6 libfribidi0 libgbm1 libgcc-s1 libgcrypt20 libgdk-pixbuf2.0-0 libglib2.0-0 libgmp10 libgnutls30 libgpg-error0 libgraphite2-3 libgssapi-krb5-2 libgtk-3-0  libharfbuzz0b libhogweed6 libice6 libidn2-0 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 liblz4-1 liblzma5 libmount1 libnettle8 libnspr4 libnss3 libp11-kit0 libpango-1.0-0 libpangocairo-1.0-0  libpangoft2-1.0-0 libpcre2-8-0 libpcre3 libpixman-1-0 libpng16-16 libselinux1 libsm6 libsqlite3-0  libsystemd0 libtasn1-6  libthai0 libunistring2 libuuid1 libwayland-client0 libwayland-cursor0 libwayland-egl1 libwayland-server0 libx11-6 libx11-xcb1 libxau6 libxcb-dri3-0 libxcb-render0 libxcb-shm0 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxdmcp6 libxext6 libxfixes3 libxft2 libxi6 libxinerama1 libxkbcommon0 libxrandr2 libxrender1 libxss1 libxtst6 lsb lsb-core libc6-i386 libxm4 xsltproc
```
<details>
<summary>If you are using Ubuntu 24.04 or Linux Mint 22, F***** might still work.</summary>

```sh
sudo apt -y install libasound2t64 libatk-bridge2.0-0t64 libatk1.0-0t64 libatspi2.0-0t64 libavahi-client3 libavahi-common3 libblkid1 libbsd0 libc6 libcairo-gobject2 libcairo2 libcom-err2 libcups2t64 libdatrie1 libdbus-1-3 libdrm2 libepoxy0 libexpat1 libffi8 libfontconfig1 libfreetype6 libfribidi0 libgbm1 libgcc-s1 libgcrypt20 libgdk-pixbuf2.0-0 libglib2.0-0t64 libgmp10 libgnutls30t64 libgpg-error0 libgraphite2-3 libgssapi-krb5-2 libgtk-3-0t64  libharfbuzz0b libhogweed6t64 libice6 libidn2-0 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 liblz4-1 liblzma5 libmount1 libnettle8t64 libnspr4 libnss3 libp11-kit0 libpango-1.0-0 libpangocairo-1.0-0  libpangoft2-1.0-0 libpcre2-8-0 libpcre3 libpixman-1-0 libpng16-16t64 libselinux1 libsm6 libsqlite3-0  libsystemd0 libtasn1-6  libthai0 libunistring5 libuuid1 libwayland-client0 libwayland-cursor0 libwayland-egl1 libwayland-server0 libx11-6 libx11-xcb1 libxau6 libxcb-dri3-0 libxcb-render0 libxcb-shm0 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxdmcp6 libxext6 libxfixes3 libxft2 libxi6 libxinerama1 libxkbcommon0 libxrandr2 libxrender1 libxss1 libxtst6 libc6-i386 libxm4 xsltproc
```
</details>
Download A**** via **rsync** (DO NOT use SMB to copy files)
```
sudo mkdir -p /share/Apps
sudo rsync -aP <your_user_name>@oztekingroup.dept.lehigh.edu:/mnt/Lab/IAC/A* /share/Apps
```

### While waiting for it, you can continue to the next section and come back later.

Copy desktop shortcut
```
cp /share/Apps/A*.desktop ~/Desktop
```
then right click on desktop shortcut -> Permissions -> Allow executing file as program

If you can't launch A*, run `chown -R oztekinlab /share/Apps/A*` and `chgrp -R oztekinlab /share/Apps/A*` to fix permission.

## Install OpenFOAM
Open a new terminal
```
sudo apt install -y curl
curl https://dl.openfoam.com/add-debian-repo.sh | sudo bash
sudo apt install -y openfoam2406-default
echo 'source /usr/lib/openfoam/openfoam2406/etc/bashrc' >> ~/.bashrc 
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
### While waiting for it, you can continue to the next section and come back later.

If the compilation fails, run `./Allwmake` for multiple times (please be patient!) until it compiles without error.

## Install Paraview
Open a new terminal
Install Paraview and its dependencies
```
sudo apt install -y paraview
```

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
wget https://download.anydesk.com/linux/anydesk_6.3.3-1_amd64.deb
sudo apt install -y ./anydesk_6.3.3-1_amd64.deb
```
Then set the password to enable unattended access.

## Install Rustdesk (in case Anydesk says you are a commercial user)
```
wget https://github.com/rustdesk/rustdesk/releases/download/1.3.1/rustdesk-1.3.1-x86_64.deb
sudo apt install -y ./rustdesk-1.3.1-x86_64.deb
```
The ID server address is
```
oztekingroup.dept.lehigh.edu
```
The public key(mandatory) is
```
+hJp1uBJ3NdUuv7A38dJYkiLSmWFvyqauwv+535vIns=
```
Then set the password to enable unattended access.

If you see any error on your existing computer, please update the key.
