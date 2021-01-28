# Gromacs workshop installation instructions

This page contains software installation instructions for Gromacs workshops. When relevant, separate instructions are given for Windows, MacOS and Linux systems.

- For the tutorial "Computing trajectories efficiently on GPUs" and the "QM/MM" part we will use an external cluster and instructions will be provided during the workshop.
- After you have gone through the installation steps below, please download the [tutorial Jupyter notebook](tutorial.ipynb), run the notebook from terminal with `jupyter-notebook tutorial.ipynb`, and execute all cells in the notebook to make sure you have everything installed correctly.

## GROMACS and Python packages

#### Perparatory steps for Windows users

We strongly recommend to use (and install if necessary) the Windows Subsystem for Linux, WSL. Inside it you will need Python 3 and the conda Python environment manager. A useful guide to doing this is found at https://github.com/kapsakcj/win10-linux-conda-how-to. Once you have WSL and conda installed, make an environment for the GROMACS tutorials.

#### Installing miniconda

Install miniconda for Python 3 by following

| Linux | MacOS | Windows|
|---|---|---|
| https://docs.conda.io/en/latest/miniconda.html#linux-installers | https://docs.conda.io/en/latest/miniconda.html#macosx-installers | https://github.com/kapsakcj/win10-linux-conda-how-to |

#### After having installed conda

Make an environment for the GROMACS tutorials with:

```bash
conda create --name gromacs-tutorials -c conda-forge -c bioconda gromacs=2020.5 matplotlib nglview notebook numpy requests pandas seaborn -c rdkit rdkit 
```

For MaxOS, we will have to stick to the older `gromacs=2019.1`

Please use a separate environment for biobb (bio-building blocks)

## (Optional) MPI-enabled GROMACS

The tutorial on replica-exchange MD simulations requires MPI-based GROMACS. Since we will not be running production MD simulations during the tutorial it is not necessary to install an MPI version, and we will be providing reference output files for non-MPI GROMACS as a backup. 
However, if you want to install MPI-based GROMACS on your own computer we provide here a general installation based on conda which should work on MacOS, Linux and WSL under Windows. If you already have compilers and an MPI library installed on your computer, you can skip the first section below or refer to the official installation instructions (https://manual.gromacs.org/documentation/current/install-guide/index.html). 

#### Install compilers, OpenMPI and CMake

```bash
conda create --name mpi
conda activate mpi
conda install -c conda-forge compilers
conda install -c conda-forge openmpi
conda install -c conda-forge cmake
```

#### Download GROMACS 2020.5

```bash
wget http://ftp.gromacs.org/pub/gromacs/gromacs-2020.5.tar.gz 
cd gromacs-2020.5
(or git clone https://gitlab.com/gromacs/gromacs.git; cd gromacs; git checkout v2020.5)
```

#### Compile GROMACS

```bash
mkdir build
cd build
cmake .. -DCMAKE_C_COMPILER=mpicc -DCMAKE_CXX_COMPILER=mpicxx   -DGMX_MPI=ON -DGMX_DOUBLE=OFF  -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON
make
make check
sudo make install
source /usr/local/gromacs/bin/GMXRC
```

You will now hopefully be able to run gmx_mpi!

You can also compile an MPI version of GROMACS by following these steps in a terminal (click "New" and select "Terminal"):

```bash
cd $HOME
wget http://ftp.gromacs.org/pub/gromacs/gromacs-2020.5.tar.gz
tar zxf gromacs-2020.5.tar.gz
cd gromacs-2020.5
mkdir build
cd build
cmake .. -DCMAKE_C_COMPILER=mpicc -DCMAKE_CXX_COMPILER=mpicxx   -DGMX_MPI=ON -DGMX_DOUBLE=OFF  -DGMX_FFT_LIBRARY=fftpack
make
```

This procedure will install `gmx_mpi` into `/home/jovyan/gromacs-2020.5/build/bin` which you can use for MPI-enabled simulations.
