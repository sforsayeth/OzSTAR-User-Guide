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


**OzSTAR** uses *hierarchical* modules, what that means is that loading a module makes more modules available by updating the module path where the module manager looks for modules. This is especially useful to avoid dependency problems, and has been used on OzSTAR to avoid compiler and OpenMPI conflicts and incompatibilities.

The following list summarises how modules work on OzSTAR:-

* There are 3 distinct categories of modules, *Core Modules*, *Compiler Modules*, and *Compiler/OpenMPI Modules*
* Core modules are meta modules, or modules built against the system compiler
* Compiler modules are built against a specific compiler
* Compiler/OpenMPI modules are built against a specific compiler/OpenMPI combination.
* Loading a module on OzSTAR requires thte **use of a version identifier**. **There are no default modules**
* Loading a module without a version identifier **will show the available versions of the module**
* Loading an **ambigous module** (A module that is built against two different compiler or OpenMPI versions) requires first **loading the parent modules(s) first**
* Loading an ambiguous module **will list the possible parent modules**
* Loading a module that is not ambigous **will automatically load it's parent hierarchy of modules if one exists**
* ```module avail``` with **no modules loaded** will list **all modules for all compiler combinations on the system**
* ```module avail``` with **a compiler or compiler/OpenMPI module** loaded will only **list modules for that compiler or compiler/OpenMPI combination**


Listing all available modules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Hierarchical modules in the context of OzSTAR use compiler and OpenMPI modules as the parent *hierarchy*. These parent modules are referred to as **toolchains** henceforth in this document. Put simply **a toolchain is just a compiler or compiler/OpenMPI combination**. 

::

    module purge

    module avail

    -------------------------------------------------------------------- Compiler: gcc 5.5.0 OpenMPI: 3.0.0 --------------------------------------------------------------------
    boost/1.66.0    fftw/2.1.5    fftw/3.3.7    hdf5/1.8.19    hdf5/1.10.1    netcdf/4.5.0    scalapack/2.0.2-openblas-0.2.20

    --------------------------------------------------------------------------- Compiler: gcc 5.5.0 ----------------------------------------------------------------------------
    openblas/0.2.20    openmpi/3.0.0

    (...)

*In this example there are two toolchain, GCC 5.5.0, and GCC 5.5.0 with OpenMPI 3.0.0*


Listing toolchain specific modules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Loading a toolchain make other modules available by extending the current module path that the module manager can find modules. On OzSTAR, loading a compiler or compiler/OpenMPI toolchain makes the modules listed by ```module avail``` list the modules available to (that is to say built or compiled against) that loaded toolchain. However ```module avail``` with no toolchain loaded, will list modules for all available toolchains on OzSTAR.

:: 

    module purge
    
    module load gcc/6.4.0

    module avail

    --------------------------------------------------------------------------- Compiler: gcc 6.4.0 ----------------------------------------------------------------------------
    cfitsio/3.420                  ipython/5.5.0-python-3.6.4    protobuf-python/3.5.1-python-2.7.14    pyzmq/16.0.4-python-2.7.14-zmq4    vim/8.0-python-2.7.14
    
    (...)

    ------------------------------------------------------------------------------- Core Modules -------------------------------------------------------------------------------
    anaconda2/5.0.1        cudnn/7.0.5-cuda-9.0.176        gcccore/system        gnu/2018.0     icc/2016.2.181-gcc-6.4.0         intel/2016.2.181-gcc-6.4.0    szip/2.1.1

    (...)

*Here you can see that loading the GCC 6.4.0 toolchain now only list the modules available for GCC 6.4.0 and the Core Modules*

::

    module load openmpi/3.0.0 

    module avail

    -------------------------------------------------------------------- Compiler: gcc 6.4.0 OpenMPI: 3.0.0 --------------------------------------------------------------------
    astropy/2.0.3-python-2.7.14    lalsuite-lalapps/6.21.0          lalsuite-lalxml/1.2.4                           scalapack/2.0.2-openblas-0.2.20

    (...)

    --------------------------------------------------------------------------- Compiler: gcc 6.4.0 ----------------------------------------------------------------------------
    cfitsio/3.420                  ipython/5.5.0-python-3.6.4    protobuf-python/3.5.1-python-2.7.14    pyzmq/16.0.4-python-2.7.14-zmq4    vim/8.0-python-2.7.14

    (...)

    ------------------------------------------------------------------------------- Core Modules -------------------------------------------------------------------------------
    anaconda2/5.0.1        cudnn/7.0.5-cuda-9.0.176        gcccore/system        gnu/2018.0     icc/2016.2.181-gcc-6.4.0         intel/2016.2.181-gcc-6.4.0    szip/2.1.1

    (...)

*Then if we load GCC 6.4.0's OpenMPI, we can see all modules available to the GCC 6.4.0 OpenMPI 3.0.0 toolchain*


On OzSTAR, the following four toolchain combinations exist:-

* GCC
* GCC/OpenMPI
* Intel
* Intel/OpenMPI

Loading modules
^^^^^^^^^^^^^^^

If a module is not ambigous, that is to say it only has one parent toolchain, then the module manager will automatically load the parent toolchain before loading your module. See below for loading ambigous modules. Just remember that you need to specify the version of the module. If you don't specify the version, the module manager will list the available versions.

::

    module purge
    
    module load python

    Lmod has detected the following error:  Couldn't find module with name python, did you mean to load one of the following?
        * python/2.7.14
        * python/3.6.4

*In this example you can see that the module manager has listed the available versions of python because we didn't specify the version*

::

    module purge
    
    module load python

    module list

**insert listed modules**
    

Loading ambiguous modules
^^^^^^^^^^^^^^^^^^^^^^^^^

A module is considered ambigous if it has more than one parent hierarchy, and the module manager is unable to automotically load the parent hierarchy. In this case a ```module load``` will mention that the load is ambiguous and then list all parent toolchain combinations. You must then load the specific toolchain manually, before being able to load the original module.

::

    module purge

    module load openblas/0.2.20 

    Lmod has detected the following error:  Can't load openblas/0.2.20 because it has more than one parent hierarchy, making this load ambiguous.

        Please load one of the following combinations before loading this module:
        * gcc/6.4.0
        * gcc/5.5.0
        * gcc/7.3.0

*In this example, we tried to load OpenBLAS, but it exists as a child of multiple GCC versions. In order to load this module, we first need to load the specific version of GCC we want before we can load the module*

::

    module purge

    module load gcc/6.4.0

    module load openblas/0.2.20

    module list

**insert listed modules**

Please note the following useful commands: ``module purge`` will allow you to unload all modules currently loaded. It is
also possible to switch between ``gcc`` and ``intel`` by typing:

::

    module swap gcc intel

which is equivalent to typing:

::

    module unload gcc
    module load intel

Another way to search for modules is with the ``module spider`` command. This command searches the entire list of possible modules.

::

    module spider

This can be practical if you are looking for a specific module. Consider the following example of commands:

::

    $ module purge

    $ module python
    Lmod has detected the following error:  These module(s) exist but cannot be loaded as requested: "python"
   Try: "module spider python" to see how to load the module(s).

You can therefore look for more information to load the module correctly:
::

    $ module spider python

    ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
      python: python/2.7.14
    ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    Description:
      Python is a programming language that lets you work more quickly and integrate your systems more effectively.


     Other possible modules matches:
        ipython, protobuf-python

    You will need to load all module(s) on any one of the lines below before the "python/2.7.14" module is available to load.

      gcc/6.4.0  openmpi/3.0.0

    Help:

      Description
      ===========
      Python is a programming language that lets you work more quickly and integrate your systems
       more effectively.

And now, we know how to load the module!
::

    $ module load gcc/6.4.0  openmpi/3.0.0 python/2.7.14

    $ module list

    Currently Loaded Modules:
      1) nvidia/384.90 (S)   3) binutils/2.30   5) gcc/6.4.0   7) openmpi/3.0.0     9) fftw/3.3.7                       11) sqlite/3.21.0
      2) slurm/17.11.2 (S)   4) gcccore/6.4.0   6) ucx/1.2.1   8) openblas/0.2.20  10) scalapack/2.0.2-openblas-0.2.20  12) python/2.7.14

      Where:
       S:  Module is Sticky, requires --force to unload or purge


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
