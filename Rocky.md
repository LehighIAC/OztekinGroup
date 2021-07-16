# OztekinGroup
Instructions to reinstall lab workstations

## Creating Installer
1. Download the latest Rocky Linux image from https://download.rockylinux.org/pub/rocky/8/isos/x86_64/Rocky-8.4-x86_64-dvd1.iso
2. Use [balenaEtcher](https://www.balena.io/etcher/) to write .iso to a flash drive. This tool is cross-platform and you can do it on any OS.
3. Boot from the flash drive. Select `UEFI` if available.
4. Install Rocky Linux. If you experience graphics issue, press `e` and append `nomodeset` then press ctrl+x to start installer. If still not working, select `Troubleshooting -> ****** in basic graphics mode`.
## Options
1. Software Selection: Choose `Workstation`, tick `Development Tools` and `Scientific Support`
2. Installation Destination: Choose the SSD and select `I will configure partitioning`. Delete all existing partitions and select `Standard Partition`. Set up 200 MB for `/boot/efi`, **at least** 60 GB for `/`(use `ext4`) and the rest to `/home`(use `ext4`). No need to have `/swap` if the RAM is large enough.
3. Disable Kdump(useless)
4. Network : Enable ethernet if available.
5. Setup root password and set username as `oztekinlab` if it's a public computer. Do not select `make this user administrator`.

## Initialization
First time boot: Log in to `root`.
Perform system update
```
dnf -y update
```
Go to `Utilities -> Disks` and select HDD. Click `Edit mount options` and switch `Use Session Defaults` to `OFF`

Reboot

## Nvidia Driver
```
dnf install -y tar bzip2 make automake gcc gcc-c++ pciutils elfutilslibelf-devel libglvnd-devel
dnf config-manager --set-enabled powertools
dnf install -y epel-release
dnf -y update
dnf config-manager --add-repo http://developer.download.nvidia.com/compute/cuda/repos/rhel8/x86_64/cuda-rhel8.repo
dnf install -y kernel-devel-$(uname -r) kernel-headers-$(uname -r) kernel kernel-core kernel-modules
dnf -y module install nvidia-driver:latest-dkms
nvidia-xconfig
```
Reboot

## Install A****(you know...)
A**** 21R1 works with Red Hat 8 and its replica.

Install prerequisites
```
mkdir -p /share/Apps
dnf -y install libpng12 libnsl
```
Login to group NAS via **SFTP** (DO NOT use SMB here)
```
sftp://<username>@oztekingroup.dept.lehigh.edu
```
Copy all files from `/mnt/Lab/gux215/A****` to `/share/Apps/A****`

While waiting for it, you can continue and come back later.

Use `chmod +x -R` to fix executable permission.

## OpenFOAM
Install pre-compiled OpenFOAM
```
dnf -y copr enable openfoam/openfoam
dnf -y install openfoam-selector
dnf -y install openfoam2012-default
```
Install Paraview
```
dnf -y install paraview --enablerepo=epel-testing
```
## swak4foam
```
su root
dnf -y install mercurial readline-devel python2-devel python3-devel
alternatives --set python /usr/bin/python3
exit
```
```
echo 'source /usr/lib/openfoam/openfoam2012/etc/bashrc $FOAM_SETTINGS' >> $HOME/.bashrc
source $HOME/.bashrc
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
su root
dnf -y install python3-pip python3-numpy gnuplot
pip3 install pyfoam
exit
```
Usage:
```
pyFoamPlotWatcher.py <logfilename>
```
## Install Anydesk & Teamviewer
```
wget https://download.anydesk.com/linux/anydesk-6.1.1-1.el8.x86_64.rpm
wget https://download.teamviewer.com/download/linux/teamviewer.x86_64.rpm
dnf -y install anydesk-6.1.1-1.el8.x86_64.rpm teamviewer.x86_64.rpm
```
Then enable unattended access.
## Customization (Optional)
If you don't need a Windows-10 like desktop environment, skip this step.

Goto https://extensions.gnome.org/ and install the following gnome-extensions

[Dash to Panel](https://extensions.gnome.org/extension/1160/dash-to-panel/)

[TopIcons Plus](https://extensions.gnome.org/extension/1031/topicons/)

[No Topleft Hot Corner](https://extensions.gnome.org/extension/118/no-topleft-hot-corner/)

Start `GNOME Tweaks`

In `Desktop` tab enable icons.

In `Extensions` tab switch on `Application menu`. Go to the setting button of `Dash to panel` and disable `Application button`

In `Windows` tab enable minimize and maximize button.

## Post-installation
1. Can't run OpenFOAM in parallel
If `mpirun` command is missing
```
echo 'module load mpi/openmpi-x86_64' >> $HOME/.bashrc
```
If `module` is missing, log out & log in.

2. Fix internal HDD ownership after reinstallation.
```
su root
chown -R <username> <path>
chgrp -R <groupname> <path>
```

3. Mount NTFS format external HDD
```
dnf -y install ntfs-3g
```
