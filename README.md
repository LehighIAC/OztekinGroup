# OztekinGroup
Instructions to reinstall lab workstations

End Of Life: 31st May 2029
## Creating Installer
1. Make sure the `Secure Boot` in UEFI BIOS is disabled.
2. Download the latest Rocky Linux image from https://download.rockylinux.org/pub/rocky/8/isos/x86_64/Rocky-8.6-x86_64-dvd1.iso
3. Use [Ventoy]([https://www.balena.io/etcher/](https://github.com/ventoy/Ventoy/releases)) to install the image. You don't need to write images to the flash drive anymore.
4. Boot from the flash drive and select Rocky Linux image.
5. Install Rocky Linux. If you experience graphics issue, press `e` and append `nomodeset` then press ctrl+x to start installer. If still not working, select `Troubleshooting -> ****** in basic graphics mode`.
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
dnf config-manager --set-enabled powertools
dnf install -y epel-release
dnf -y update
dnf config-manager --add-repo http://developer.download.nvidia.com/compute/cuda/repos/rhel8/x86_64/cuda-rhel8.repo
dnf -y module install nvidia-driver:470
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
Download A**** via **rsync** (DO NOT use SMB here)
```
rsync -aP username@oztekingroup.dept.lehigh.edu:/mnt/Lab/gux215/A**** /share/Apps
```

While waiting for it, you can continue and come back later.

Use `chown -R oztekinlab A****` and `chgrp -R oztekinlab A****` to fix permission.

## OpenFOAM
Install pre-compiled OpenFOAM
```
dnf -y copr enable openfoam/openfoam
dnf -y install openfoam-selector
dnf -y install openfoam2112-default
```
Install Paraview
```
dnf -y install qt5-qttools-libs-help
dnf -y install paraview
```
Source OpenFOAM folder
```
echo 'module load mpi/openmpi-x86_64' >> $HOME/.bashrc
echo 'source /usr/lib/openfoam/openfoam2112/etc/bashrc $FOAM_SETTINGS' >> $HOME/.bashrc
source $HOME/.bashrc
```
If `module` is missing, log out & log in.
## swak4foam
```
su root
dnf -y install mercurial readline-devel python2-devel python3-devel
alternatives --set python /usr/bin/python3
exit
```
```
cd $HOME
mkdir OpenFOAM
cd OpenFOAM
hg clone http://hg.code.sf.net/p/openfoam-extend/swak4Foam swak4Foam
cd swak4Foam
hg update 816fd4168b87
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
1. Mount NTFS format external HDD
```
dnf -y install ntfs-3g
```

2. Fix internal HDD ownership after reinstallation.
```
su root
chown -R <username> <path>
chgrp -R <groupname> <path>
```
