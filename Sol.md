# Compile OpenFOAM-v2406 on Lehigh Sol/Hawk
1. Download OpenFOAM-v2406 and patch
```bash
cd $HOME
mkdir OpenFOAM
cd OpenFOAM
git clone https://develop.openfoam.com/Development/openfoam.git OpenFOAM-v2406
cd OpenFOAM-v2406
git checkout OpenFOAM-v2406
wget https://github.com/LehighIAC/OztekinGroup/raw/main/Sol-v2406.patch
git apply Sol-v2406.patch
```

2. Setting up the environment on head node
```bash
echo "module load gcc" >> $HOME/.bashrc
echo "module load mpich" >> $HOME/.bashrc
echo "source $HOME/OpenFOAM/OpenFOAM-v2406/etc/bashrc" >> $HOME/.bashrc
source $HOME/.bashrc
```

3. Compile OpenFOAM
Due to limitations on the head node, only two cores are allowed and you'll have to wait overnight. It is recommended to use `tmux` during the building process.
```bash
module load cmake boost adios2 fftw hypre kahip metis petsc scotch
foam
export MPI_ARCH_PATH="/share/Apps/lusoft/opt/spack/linux-centos8-haswell/gcc-9.3.0/mpich/3.3.2-rc3lxvt"
./Allwmake -j 2 -s -l
./Allwmake -j 2 -s -l
```

# Sample SLURM Script
You'll have to use `srun` command. `mpirun` or OpenFOAM `runParallel` command does not work on Sol/Hawk. Make sure `system/decomposeParDict` is consistent with the number of cores you requested.
```bash
#!/bin/bash
#SBATCH --partition=hawkcpu
#SBATCH -t 24:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=52
#SBATCH --job-name=[YOUR_JOBNAME]
#SBATCH --mail-type=ALL
#SBATCH --mail-user=[YOUR_EMAIL]

source /etc/profile.d/zlmod.sh
module load gcc
module load mpich
module load scotch metis
source /home/[YOUR_USERNAME]/OpenFOAM/OpenFOAM-v2406/etc/bashrc

cd ${SLURM_SUBMIT_DIR}
srun [YOUR_OPENFOAM_COMMAND] -parallel > [YOUR_LOGFILE_NAME] 2>&1 
```