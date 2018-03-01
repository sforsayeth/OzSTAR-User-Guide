.. highlight:: rst

Environment Modules
====================

`Environment modules <http://modules.sourceforge.net/>`_ allow for dynamically modifying ones shell environment to provide access to required packages. Managing your environment manually can be cumbersome, using modules is highly recommended and is standard practice on OzSTAR.

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

Compiler specific
---------------------

**OzSTAR** now includes groups of modules organised around a specific compiler (namely, intel and gcc). Each group provides
modules compiled using one of these compiler. These are accessed via the following commands.

For modules compiled with the intel compiler:
::

    module load intel

Then, if you run a ``module avail``, you should see at the beginning of the list of modules available specific to intel:
::

    module avail

    --------------------------------------------------- /apps/skylake/modulefiles/all/mpi/intel/2018.1.163-gcc-6.4.0/openmpi/3.0.0 ----------------------------------------------------
       boost/1.66.0    hdf5/1.8.19    hdf5/1.10.1 (D)    imkl/2018.1.163 (L)    netcdf/4.5.0

    -------------------------------------------------------- /apps/skylake/modulefiles/all/compiler/intel/2018.1.163-gcc-6.4.0 --------------------------------------------------------
       openmpi/3.0.0 (L)

    (...)


Similarly, for GCC, you can run the following:
::

    module load gcc

And ``module avail`` should display something like the following:
::

    ---------------------------------------------------------------- /apps/skylake/modulefiles/all/compiler/gcc/6.4.0 -----------------------------------------------------------------
       cfitsio/3.420    clang/5.0.1    framel/8.30    gsl/2.4    llvm/5.0.1    openblas/0.2.20    openmpi/3.0.0    qt/4.8.7    sqlite/3.21.0

   (...)

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
