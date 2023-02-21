# OpenFOAM-v2212
## Environment Setup
SSH to fj-debug nodes
### Dependencies
```
module load fujitsu/compiler/4.8
module load fftw3/fujitsu/sve-1.0.0
module load petsc/fujitsu4.8/3.18.3
module load boost/1.71.0
export MPI_ROOT=/opt/FJSVstclanga/cp-1.0.21.02/
```
### Build only
```
module load cmake/3.24.2
```
## Download OpenFOAM-v2212
Start from the folder you want, e.g. `cd /lustre/software/openFoam`
```
git clone https://develop.openfoam.com/Development/openfoam.git OpenFOAM-v2212
cd OpenFOAM-v2212
git checkout OpenFOAM-v2212
git submodule init
git submodule update
source etc/bashrc
```
### Apply A64FX patch
```
wget https://github.com/BrushXue/OztekinGroup/raw/main/Ookami/fujitsu.patch
git apply fujitsu.patch
```
## Download ThirdParty-v2212
```
cd ..
git clone https://develop.openfoam.com/Development/ThirdParty-common.git ThirdParty-v2212
cd ThirdParty-v2212
git checkout v2212
```
### Patch ADIOS2 script
```
wget https://github.com/BrushXue/OztekinGroup/raw/main/Ookami/ADIOS2.patch
git apply ADIOS2.patch
```
## Build Third Party
```
wmRefresh
export WM_NCOMPPROCS=24
```
### Build Scotch
```
wget https://gforge.inria.fr/frs/download.php/file/38352/scotch_6.1.0.tar.gz
mkdir -p sources/scotch
tar -xvf scotch_6.1.0.tar.gz --directory sources/scotch
./makeSCOTCH
```
### Build Metis
```
wget http://glaros.dtc.umn.edu/gkhome/fetch/sw/metis/metis-5.1.0.tar.gz
mkdir -p sources/metis
tar -xvf metis-5.1.0.tar.gz --directory sources/metis
./makeMETIS
```
### Build CGAL
```
wget https://github.com/CGAL/cgal/releases/download/releases/CGAL-4.14.3/CGAL-4.14.3.tar.xz
mkdir -p sources/cgal
tar -xvf CGAL-4.14.3.tar.xz --directory sources/cgal
./makeCGAL -with-lib gmp-none mpfr-none
```
### Build ADIOS2
Currently not available due to `libfabric` issue. Skip this step for now
```
wget https://github.com/ornladios/ADIOS2/archive/v2.8.3.tar.gz
mkdir -p sources/adios
tar -xvf v2.8.3.tar.gz --directory sources/adios
./makeAdios2
```
## Build OpenFOAM
```
cd $WM_PROJECT_DIR
tmux
./Allwmake -j 24 -l
```
Wait for ~4 hours. Run Allwamke again to check if all libraries/apps are built
```
./Allwmake -j 24 -l
```
The module should load the following commands:
```
module load fujitsu/compiler/4.8
module load fftw3/fujitsu/sve-1.0.0
module load petsc/fujitsu4.8/3.18.3
module load boost/1.71.0
export MPI_ROOT=/opt/FJSVstclanga/cp-1.0.21.02/ 
source ???/OpenFOAM-v2212/etc/bashrc
```
