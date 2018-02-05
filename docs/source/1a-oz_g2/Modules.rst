.. highlight:: rst

Environment Modules
====================

`Environment modules <http://modules.sourceforge.net/>`_ allow for dynamically modifying ones shell environment to provide access to required packages. Managing your environment manually can be cumbersome, using modules is highly recommended and is standard practice on gStar.

The structure of a module command is:
::

    module <command> [arguments]

There are many available commands, to see a list simply run:
::

    module

To get you started, you will first need to be able to see which software packages are available. To do so, run:
::

    module avail

This will display a list of all installed software packages. To load a package, preparing your environment for its use, run:
::

    module load <package name>

For example, if I wanted to use a 64bit version of OpenMPI compiled with the GNU tool chain, I could use:
::

    module load openmpi/x86_64/gnu/1.4.4

You’ll notice that, after running “module avail”, some of the package names have “(default)” written next to them. This indicates that you don’t need to type the whole name to enable that package. Using the previous OpenMPI example, I could achieve the same results by running:
::

    module load openmpi

OzSTAR vs Green II
---------------------

There exists slight differences between how modules are handled on OzSTAR and Green II.


**OzSTAR** now includes generic and optimized versions of modules. For example, by typing ``module avail``,
you should see a set of modules under `skylake <https://en.wikipedia.org/wiki/Skylake_(microarchitecture)>`__, which are optimised for specific hardware; the same modules are also available in a generic form::

    % module avail

    ------------------------------------------------------------------------ /apps/skylake/modulefiles/all -------------------------------------------------------------------------
       fftw/2.1.5-gompi-2018    fftw/3.3.7-gompi-2018 (D)    gsl/2.4-gcc-6.4.0 (D)    python/2.7.14-foss-2018    python/3.6.4-foss-2018 (D)    sqlite/3.21.0-gcc-6.4.0 (D)

    ------------------------------------------------------------------------ /apps/generic/modulefiles/all -------------------------------------------------------------------------
       astropy/2.0.3-foss-2018-python-2.7.14        git/2.16.0                                      openblas/0.2.20-gcc-6.4.0
       astropy/2.0.3-foss-2018-python-3.6.4  (D)    gompi/2018                                      openmpi/3.0.0-gcc-6.4.0
       (...)
       fftw/2.1.5-gompi-2018                        iimpi/2016.2.181-gcc-6.4.0                      szip/2.1.1-foss-2018
       fftw/3.3.7-gompi-2018                        imkl/11.3.2.181-iimpi-2016.2.181-gcc-6.4.0      ucx/1.2.1
       foss/2018                                    impi/5.1.3.181-iccifort-2016.2.181-gcc-6.4.0    vasp/5.4.1-gpu-intel-2016.2.181-gcc-6.4.0
       gcc/6.4.0                                    intel/2016.2.181-gcc-6.4.0                      vasp/5.4.1-intel-2016.2.181-gcc-6.4.0      (D)
       gcccore/6.4.0                                llvm/5.0.1-foss-2018




On **Green II**, ``module avail`` will provide a list of all versions of available modules::

    % module avail

    ------------------------------------------------------------------------ /usr/local/modules/modulefiles ------------------------------------------------------------------------
    2dfdr/x86_64/5.35                                gromacs/x86_64/gnu/4.6.3-cpu                     openmpi/x86_64/gnu/1.10.2-psm
    2dfdr/x86_64/6.28                                gromacs/x86_64/gnu/5.0.5                         openmpi/x86_64/gnu/1.4.4
    adf/2013.01                                      gsl/x86_64/gnu/1.13-1                            openmpi/x86_64/gnu/1.4.5
    adf/2016.107                                     gsl/x86_64/gnu/1.15                              openmpi/x86_64/gnu/1.6
    (...)
    google-protocol-buffer/2.5                       opencv/x86_64/gnu/3.3.0                          xz/x86_64/5.2.2
    grace/x86_64/gnu/5.1.23                          OpenFOAM/x86_64/gnu/2.2.2                        yorick/x86_64/gnu/2.1
    gromacs/x86_64/gnu/4.6.1                         OpenFOAM/x86_64/gnu/2.4.0                        yorick/x86_64/gnu/2.1.06
    gromacs/x86_64/gnu/4.6.3                         OpenFOAM/x86_64/gnu/4.1.0
