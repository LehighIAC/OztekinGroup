# OpenFOAM-v2212
## Environment Setup
SSH to fj-debug nodes
### Dependencies
```
module load fujitsu/compiler/4.8
module load fftw3/fujitsu/sve-1.0.0
module load petsc/fujitsu4.8/3.18.3
export MPI_ROOT=/opt/FJSVstclanga/cp-1.0.21.02/
```
### Build only
```
module load cmake/3.24.2
```
## Download OpenFOAM
```
git clone https://develop.openfoam.com/Development/openfoam.git OpenFOAM-v2212
cd OpenFOAM-v2212
git checkout OpenFOAM-v2212
git submodule init
git submodule update
source etc/bashrc
```
### Backport [CGAL patch](https://develop.openfoam.com/Development/openfoam/-/commit/03ab6c1a9dd38bad53f95ce84218660b39b6116b)
```
wget https://github.com/BrushXue/OztekinGroup/Ookami/raw/main/cgal.patch
git apply cgal.patch
```
### Apply A64FX patch
```
wget https://github.com/BrushXue/OztekinGroup/Ookami/raw/main/fujitsu.patch
git apply fujitsu.patch
```
## Download ThirdParty
```
cd ..
git clone https://develop.openfoam.com/Development/ThirdParty-common.git ThirdParty-v2212
cd ThirdParty-v2212
git checkout v2212
```
### Patch ADIOS2 script
```
wget https://github.com/BrushXue/OztekinGroup/Ookami/raw/main/adios.patch
git apply adios.patch
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
### Build CGAL/boost
```
wget https://sourceforge.net/projects/boost/files/boost/1.74.0/boost_1_74_0.tar.bz2
wget https://github.com/CGAL/cgal/releases/download/v5.5.1/CGAL-5.5.1.tar.xz
mkdir -p sources/boost
mkdir -p sources/cgal
tar -xvf boost_1_74_0.tar.bz2 --directory sources/boost
tar -xvf CGAL-5.5.1.tar.xz --directory sources/cgal
./makeCGAL gmp-none mpfr-none
```
### Build ADIOS2
Currently not available due to `libfabric` issue. Also need to disable `$WM_PROJECT_DIR/etc/config.sh/adios2`
```
wget https://github.com/ornladios/ADIOS2/archive/v2.8.3.tar.gz
mkdir -p sources/adios
tar -xvf v2.8.3.tar.gz --directory sources/adios
./makeAdios2
```
## Build OpenFOAM
```
cd $WM_PROJECT_DIR
./Allwmake -j 24 -l
#Run again to check if all libraries/apps are built
./Allwmake -j 24 -l
```
wait for ~8 hours.

The module should load the following
```
module load fujitsu/compiler/4.8
module load fftw3/fujitsu/sve-1.0.0
module load petsc/fujitsu4.8/3.18.3
export MPI_ROOT=/opt/FJSVstclanga/cp-1.0.21.02/ 
source ???/OpenFOAM-v2212/etc/bashrc
```
