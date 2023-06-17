# In general

The instructions below  ask you to verify whether you have particular software
installed, and if not, to install it.

A few things are worth keeping in mind:

* We try to support multiple platforms and different software environments, e.g., laptops, workstations and HPC systems running Linux, Mac or Windows. Foreseeing all possible combinations is tough, so please report back if you find anything unclear or not working. We will try to make it work.

* On some systems (in particular HPC clusters), you may need to load additional modules for some software to become available.
  On these systems, before you install anything new, try `module avail` to see whether the software you need is available,
  and if so, load it with `module load <MODULENAME>`

* There are generally two ways to install new software components:
  - at system level through a package manager (e.g., `apt` on Debian/Ubuntu Linux) or an official installer
  - in user space using Anaconda

  Both options are fine. If you are working on a system where you do not have root/administrator permissions,
  or if you are unsure which option is best for you, we recommend installing through Ananconda,
  as that does not make any system-level changes.

* We will work a lot in command line terminals. If you are working on Windows, you can open one with the "Command Prompt" (click the Start button and type `cmd`).

# Recent changes

* 2023-06-23: also install scipy (to do so separately: `conda install scipy -c conda-forge`)
* 2023-06-22: added [info on installing MIZER](#external-biogeochemical-models)

# Installation

## Start from an empty directory

As part of the installation we will create new directories with source codes and build trees.
Before you begin, `cd` to a directory where you would like these new directories to be created.

## Anaconda

Check if you have [Anaconda](https://new.anaconda.com/products/distribution) or [Miniconda](https://docs.conda.io/en/latest/miniconda.html) by executing `conda --version`

* If this fails (you get `"command not found"` or `"conda" is not recognized as an internal or external command`), install Miniconda on [Linux](https://conda.io/projects/conda/en/stable/user-guide/install/linux.html), [Windows](https://conda.io/projects/conda/en/stable/user-guide/install/windows.html), or [Mac](https://conda.io/projects/conda/en/stable/user-guide/install/macos.html). This does not require root/administrator privileges. Allow the installer to initialize the conda environment, unless you know you want to control this manually.​
* If `conda --version` succeeds (you get the version number of Anaconda), great. In that case,
  it is still good practice to make sure conda is up to date: execute `conda update conda`.
  If that command fails because you do not have the required permissions (that may happen on a cluster with centralized Anaconda installation),
  we recommend you install your own copy of Miniconda (see previous item) in user space, for instance, in your homedir.

Now, create a new conda environment​:

`conda create -n neccton-fabm`

If that succeeds, activate the new environment:

`conda activate neccton-fabm`

This will prefix your command prompt with `(neccton-fabm)` to show that you are currently in the "neccton-fabm" conda environment. Any packages installed in this environment with `conda install ...` will be isolated from the rest of the system. If you ever want to delete this environment at a later stage, you can do so with `conda env remove -n neccton-fabm`.

*In all sections below, we assume you are still in this environment!*
If you leave it by closing your terminal or executing `conda deactivate`, you will have to repeat `conda activate neccton-fabm` before executing any of the conda commands below.

## Python packages for visualization

Execute:

```
conda install jupyterlab netcdf4 ipympl scipy -c conda-forge
pip install pyncview
```

## Fortran compiler

On Linux and Mac you will often already have a suitable Fortran compiler installed. Try:

`gfortran --version`

If this succeeds and returns a version 6 or higher, you are good to go. If it fails or returns a lower version, you can install a more recent version with:

`conda install fortran-compiler -c conda-forge`

On Windows, we recommend using [Microsoft Visual Studio Community](https://visualstudio.microsoft.com/) with the [Intel Fortran Compiler](https://www.intel.com/content/www/us/en/developer/articles/tool/oneapi-standalone-components.html#fortran). Note that you need administrator permissions to install these.

## CMake

Try:

`cmake --version`

If this succeeds and returns a version 3.12 or higher, you are good to go. If it fails or returns a lower version, install CMake. To do so with Anaconda,:

`conda install cmake -c conda-forge`

## NetCDF

Try:

`nf-config --version`

If this fails, install NetCDF with Fortran support. To do so with Anaconda:

`conda install netcdf-fortran -c conda-forge`

## Git

Most systems, with the exception of Windows, will have this already installed. Try:

`git --version`

If this fails, you can install git in your Anaconda environment:

`conda install git -c conda-forge`

## FABM

To clone [the repository with the FABM source code](https://fabm.net/code):

`git clone https://github.com/fabm-model/fabm.git`

This will create a new directory `fabm` with the source code.

## External biogeochemical models

If you intend to use FABM with biogeochemical models that are not distributed with it, you need to download or clone these separately.
For instance:

* ERSEM: `git clone https://github.com/pmlmodelling/ersem.git`
* PISCES: `git clone https://github.com/BoldingBruggeman/fabm-pisces.git pisces`
* MIZER: `git clone https://github.com/pmlmodelling/fabm-mizer.git mizer`

When you build FABM, you have to point to these external models by specifying `-DFABM_INSTITUTES="<INSTITUTE-NAMES>"`
in the call to cmake (see below). Here, `<INSTITUTE-NAMES>` is a semicolon-separated list of institute names, which defaults to `akvaplan;au;bb;csiro;ersem;examples;gotm;iow;jrc;msi;nersc;niva;pclake;pml;selma;su;uhh`. Each of these names can either be the name of a subdirectory under [`<FABM>/src/models`](https://github.com/fabm-model/fabm/tree/master/src/models) or a name of an external biogeochemical model codebase. For each external codebase, you need to point FABM to the directory with the source code by also specifying `-DFABM_<INSTITUTE>_BASE=<DIR>`. For instance, to build FABM with all internally available models as well as ERSEM, PISCES and MIZER, you would add arguments:

`-DFABM_INSTITUTES="akvaplan;au;bb;csiro;ersem;examples;gotm;iow;jrc;msi;nersc;niva;pclake;pml;selma;su;uhh;pisces;mizer" -DFABM_ERSEM_BASE=<ERSEM-DIR> -DFABM_PISCES_BASE=<PISCES-DIR>  -DFABM_MIZER_BASE=<MIZER-DIR>`

In the instructions below, these additional arguments will be indicated by `<EXTRA-FABM-ARGS>`. There, `<ERSEM-DIR>`, `<PISCES-DIR>`, and `<MIZER-DIR>` would typically be `../../ersem`, `../../pisces` and `../../mizer`, respectively.

## GOTM (1D water column model)

To clone [the repository with the GOTM source code](https://github.com/gotm-model/code):

`git clone -b v6.0 --recurse-submodules https://github.com/gotm-model/code.git gotm`

This will create a new directory `gotm` with the source code.

## Building and installing GOTM-FABM

```bash
mkdir build
cd build
mkdir gotm
cd gotm
cmake ../../gotm -DFABM_BASE=../../fabm <EXTRA-FABM-ARGS>
make install
cd ../..
```

On Windows with Visual Studio/Intel Fortran, replace `make install` with `cmake --build . -t install --config Release`.

On Linux and Mac, this will place the gotm executable at `$HOME/local/gotm/bin/gotm`.
On Windows, it will be placed in `%LOCALAPPDATA%\bin\gotm.exe`.

## Building and installing pyfabm

```bash
cd build
mkdir pyfabm
cd pyfabm
cmake ../../fabm/src/drivers/python <EXTRA-FABM-ARGS>
make install
cd ../..
```

On Windows with Visual Studio/Intel Fortran, replace `make install` with `cmake --build . -t install --config Release`.

## Source code editor

We recommend using a code editor that knows about Fortran and Python.
If you do not have one yet, you could consider [Visual Studio Code](https://code.visualstudio.com/).

# After source code changes

Revisit the GOTM and pyfabm build directories and rebuild/reinstall:

```
cd build/gotm
make install
cd ../pyfabm
make install
```