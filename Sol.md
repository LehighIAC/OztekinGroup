# Compile OpenFOAM v2112 on Lehigh Sol
1. Download OpenFOAM v2112
```bash
cd $HOME
mkdir OpenFOAM
cd OpenFOAM
git clone https://develop.openfoam.com/Development/openfoam.git OpenFOAM-v2112
cd OpenFOAM-v2112
git checkout OpenFOAM-v2112
wget https://github.com/BrushXue/OztekinGroup/raw/main/OpenFOAM-Sol.patch
git apply OpenFOAM-Sol.patch
git submodule init
git submodule update
```

2. Environment Setup
```bash
# Use gcc and mpich for best compatibility across all clusters
echo "module load gcc" >> $HOME/.bashrc
echo "module load mpich" >> $HOME/.bashrc
echo "source $HOME/OpenFOAM/OpenFOAM-v2112/etc/bashrc" >> $HOME/.bashrc
source $HOME/.bashrc
# These modules are used during compilation
# Load them in SLURM scripts as needed
module load cmake boost adios2 fftw hypre kahip metis petsc scotch
```

3. Compile OpenFOAM
```bash
foam
export MPI_ARCH_PATH="/share/Apps/lusoft/opt/spack/linux-centos8-haswell/gcc-9.3.0/mpich/3.3.2-rc3lxvt"
# Limited to 2 cores on the head node, very slow
./Allwmake -j 2 -s -l
# Run again and check the log file
./Allwmake -j 2 -s -l
wmRefresh
```

4. swak4Foam (Optional)
```bash
foam
cd ..
hg clone http://hg.code.sf.net/p/openfoam-extend/swak4Foam swak4Foam
cd swak4Foam
hg update develop
# No idea why but the error message indicates this
mkdir $HOME/JOB_TMPDIR
./maintainanceScripts/compileRequirements.sh
export WM_NCOMPPROCS=2
./Allwmake > log.make 2>&1
# Run again, ignore python plugin error
./Allwmake > log.make 2>&1
```
# Sample SLURM Script
```bash
#!/bin/bash
#SBATCH --partition=enge
#SBATCH -t 48:00:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=36
#SBATCH --job-name YOUR_JOBNAME
#SBATCH --mail-type=ALL
#SBATCH --mail-user=YOUR_EMAIL

source /etc/profile.d/zlmod.sh
module load gcc
module load mpich
source /home/YOUR_USERNAME/OpenFOAM/OpenFOAM-v2112/etc/bashrc

cd ${SLURM_SUBMIT_DIR}
decomposePar
srun renumberMesh -overwrite -parallel > renumberMesh.log
srun viscoelasticFluidFoam -parallel > viscoelasticFluidFoam.log
```
