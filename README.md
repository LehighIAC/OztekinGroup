# OztekinGroup
Instructions to reinstall lab workstations

End of life: June 30th, 2024
## Creating Installer
1. Download the latest CentOS 7 image from [Lehigh mirror](http://linux.cc.lehigh.edu/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-2009.iso) for the fastest speed.
2. Use [balenaEtcher](https://www.balena.io/etcher/) to write .iso to a flash drive. This tool is cross-platform and you can do it on any OS.
3. Boot from the flash drive. Select `UEFI` if available.
4. Install CentOS 7. If you experience graphics issue, press `e` and append `nomodeset` then press ctrl+x to start installer. If still not working, select `Troubleshooting -> CentOS 7 in basic graphics mode`.
## Options
1. Software Selection: `Development and Creative Workstation` plus `Development Tools`
2. Installation Destination: Choose the SSD and select `I will configure partitioning`. Delete all existing partitions and select `Standard Partition`. Set up 200 MB for `/boot/efi`, **at least** 60 GB for `/`(use `ext4`) and the rest to `/home`(use `ext4`). No need to have `/swap`
3. Disable Kdump(useless)
4. Network : Enable ethernet if available.
5. Setup root password and set username as `oztekinlab` if it's a public computer. Do not select `make this user administrator`.

## Initialization
First time boot: Log in to `root`. Click the gear and select `GNOME` instead of `GNOME Classic`.
Perform system update
```
yum -y update
```
Go to `Utilities -> Disks` and select HDD. Click `Edit mount options` and switch `Use Session Defaults` to `OFF`

Reboot

## Nvidia Driver
Import ELRepo
```
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
yum -y install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm
```
Install Nvidia **390** driver(Latest version may not work for some GPUs)
```
yum -y install kmod-nvidia-390xx
nvidia-xconfig
```
Reboot

## Install A****(you know...)
Currently only A**** 20R2 works. If the future version supports RHEL8 I'll update this accordingly.
```
mkdir -p /share/Apps
yum -y install libpng12
```
Login to group NAS
```
smb://<username>@oztekingroup.dept.lehigh.edu
```
Copy all files from `/mnt/Lab/gux215/a****` to `/share/Apps`

While waiting for it, you can continue and come back later.

Use `chmod +x -R` to fix executable permission.

## OpenFOAM
Install pre-compiled OpenFOAM
```
yum -y install epel-release
yum -y update
yum -y install yum-plugin-copr
yum -y copr enable openfoam/openfoam
yum -y install openfoam-selector
yum -y install openfoam2012-default
```
Install legacy Paraview
```
yum -y install paraview
```
Download alternative Paraview binary
```
cd /usr/lib/openfoam/
wget -O ParaView-5.9.0.tar.gz "https://www.paraview.org/paraview-downloads/download.php?submit=Download&version=v5.9&type=binary&os=Linux&downloadFile=ParaView-5.9.0-MPI-Linux-Python3.8-64bit.tar.gz"
tar -xzf ParaView-5.9.0.tar.gz 
mv ParaView-5.9.0-MPI-Linux-Python3.8-64bit/ ParaView-5.9.0
rm ParaView-5.9.0.tar.gz 
```
## swak4foam
```
su root
yum -y install mercurial readline-devel python-devel
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
yum -y install python-pip numpy gnuplot
pip install pyfoam
exit
```
Usage:
```
pyFoamPlotWatcher.py <logfilename>
```
## Install Anydesk & Teamviewer
```
wget https://download.anydesk.com/linux/anydesk-6.1.0-1.el7.x86_64.rpm
wget https://download.teamviewer.com/download/linux/teamviewer.x86_64.rpm
yum -y install anydesk-6.1.0-1.el7.x86_64.rpm teamviewer.x86_64.rpm
```
Then enable unattended access.
## Customization (Optional)
If you don't need a Windows-10 like desktop environment, skip this step.
```
yum -y install chrome-gnome-shell
```
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

2. Activate newer ParaView (optional)
```
echo 'export PATH=/usr/lib/openfoam/ParaView-5.9.0/bin:$PATH' >> $HOME/.bashrc
```
You may see a lot of warning messages.

3. Fix internal HDD ownership after reinstallation.
```
su root
chown -R <username> <path>
chgrp -R <groupname> <path>
```

4. Mount NTFS format external HDD
```
yum -y install ntfs-3g
```
