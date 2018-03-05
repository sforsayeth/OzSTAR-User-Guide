.. highlight:: rst

Environment Modules
====================

`Environment modules <http://modules.sourceforge.net/>`_ allow for dynamically modifying ones shell environment to provide access to required software packages. Managing your environment manually can be cumbersome, using modules is highly recommended and is standard practice on OzSTAR.

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


The OzSTAR module manager (Lmod) also has a a convenient tool called ```ml``` (taken from `<http://lmod.readthedocs.io/en/latest/010_user.html#ml-a-convenient-tool>`_)

For those of you who can’t type the mdoule, moduel, err module command correctly, Lmod has a tool for you. With ml you won’t have to type the module command again. The two most common commands are ```module list``` and ```module load <something>``` and ml does both:

::

    ml

means module list. And:

::

    ml foo
    
means module load foo while:

::

    ml -bar

means module unload bar. It won’t come as a surprise that you can combine them:

::

    ml foo -bar

means module unload bar; module load foo. You can do all the module commands:

::

    ml spider
    ml avail
    ml show foo

If you ever have to load a module name spider you can do:

::

    ml load spider


OzSTAR Module Documentation
---------------------------

**OzSTAR** uses *hierarchical* modules, what that means is that loading a module makes more modules available by updating the module path where the module manager looks for modules. This is especially useful to avoid dependency problems, and has been used on OzSTAR to avoid compiler and OpenMPI conflicts and incompatibilities.

The following list summarises how modules work on OzSTAR:-

* There are 3 distinct categories of modules, *Core Modules*, *Compiler Modules*, and *Compiler/OpenMPI Modules*
* Core modules are meta modules, or modules built against the system compiler
* Compiler modules are built against a specific compiler
* Compiler/OpenMPI modules are built against a specific compiler/OpenMPI combination.
* Loading a module on OzSTAR requires thte **use of a version identifier**. **There are no default modules**
* Loading a module without a version identifier **will show the available versions of the module**
* Loading an **ambigous module** (A module that is built against two different compiler or OpenMPI versions) requires first **loading the desired parent modules(s) first**
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

*In this example there are two toolchains, GCC 5.5.0, and GCC 5.5.0 with OpenMPI 3.0.0*


Listing toolchain specific modules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Loading a toolchain makes other modules available by extending the current module path where the module manager can find modules. On OzSTAR, loading a compiler or compiler/OpenMPI toolchain makes the modules listed by ```module avail``` list the modules available to (that is to say built or compiled against) that loaded toolchain. However ```module avail``` with no toolchain loaded, will list modules for all available toolchains on OzSTAR.

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

*Here you can see that loading the GCC 6.4.0 toolchain now only lists the modules available for GCC 6.4.0 and the Core Modules*

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

*Then if we load GCC 6.4.0's OpenMPI 3.0.0, we can see all modules available to the GCC 6.4.0 OpenMPI 3.0.0 toolchain*


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
    
    module load python/2.7.14

    module list

    Currently Loaded Modules:
      1) nvidia/.384.90 (H,S)   3) binutils/2.30   5) gcc/6.4.0       7) python/2.7.14
      2) slurm/.latest  (H,S)   4) gcccore/6.4.0   6) sqlite/3.21.0

    Where:
     S:  Module is Sticky, requires --force to unload or purge
     H:             Hidden Module
    

Loading ambiguous modules
^^^^^^^^^^^^^^^^^^^^^^^^^

A module is considered ambigous if it has more than one parent hierarchy, and the module manager is unable to automatically load the parent hierarchy. In this case a ```module load``` will mention that the load is ambiguous and then list all parent toolchain combinations. You must then load the specific toolchain you want to use manually, before being able to load the original module.

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

    Currently Loaded Modules:
      1) slurm/.latest  (H,S)   3) binutils/2.30   5) gcc/6.4.0
      2) nvidia/.384.90 (H,S)   4) gcccore/6.4.0   6) openblas/0.2.20

      Where:
       S:  Module is Sticky, requires --force to unload or purge
       H:             Hidden Module

Other useful commands
^^^^^^^^^^^^^^^^^^^^^

Please note the following useful commands: ``module purge`` will allow you to unload all modules currently loaded. It is
also possible to switch between ``gcc`` and ``intel`` by typing:

::

    module swap gcc/6.4.0 intel/2018.1.163-gcc-6.4.0

which is equivalent to typing:

::

    module unload gcc/6.4.0
    module load intel/2018.1.163-gcc-6.4.0

Another way to search for modules is with the ``module spider`` command. This command searches the entire list of possible modules. Consider the following examples:-
::

    module spider
    
    -----------------------------------------------------------------------------------------------------------------------
    The following is a list of the modules currently available:
    -----------------------------------------------------------------------------------------------------------------------
      anaconda2: anaconda2/5.0.1
        Built to complement the rich, open source Python community, the Anaconda platform provides an enterprise-ready
        data analytics platform that empowers companies to adopt a modern open data science analytics architecture. 

      anaconda3: anaconda3/5.0.1
        Built to complement the rich, open source Python community, the Anaconda platform provides an enterprise-ready
        data analytics platform that empowers companies to adopt a modern open data science analytics architecture. 

      astropy: astropy/2.0.3-python-2.7.14, astropy/2.0.3-python-3.6.4
        The Astropy Project is a community effort to develop a single core package for Astronomy in Python and foster
        interoperability between Python astronomy packages.

    (...)

*Here you can see module spider will list all modules available on OzSTAR*

::

    module spider python

    -----------------------------------------------------------------------------------------------------------------------
      python:
    -----------------------------------------------------------------------------------------------------------------------
        Description:
          Python is a programming language that lets you work more quickly and integrate your systems more effectively.

        Versions:
            python/2.7.14
            python/3.6.4
        Other possible modules matches:
        ipython  protobuf-python

    -----------------------------------------------------------------------------------------------------------------------
    To find other possible module matches execute:

        $ module -r spider '.*python.*'

    -----------------------------------------------------------------------------------------------------------------------
      For detailed information about a specific "python" module (including how to load the modules) use the module's full name.
      For example:

        $ module spider python/3.6.4
    -----------------------------------------------------------------------------------------------------------------------

*Here you can see module spider can list information about a specific module*

::

    module spider python/2.7.14

    -----------------------------------------------------------------------------------------------------------------------
      python: python/2.7.14
    -----------------------------------------------------------------------------------------------------------------------
      Description:
        Python is a programming language that lets you work more quickly and integrate your systems more effectively.


      You will need to load all module(s) on any one of the lines below before the "python/2.7.14" module is available to load.

        gcc/6.4.0
 
      Help:
      
        Description
        ===========
        Python is a programming language that lets you work more quickly and integrate your systems
         more effectively.
      
      
        More information
        ================
         - Homepage: http://python.org/
      
      
        Included extensions
        ===================
        cryptography-2.1.4, Cython-0.27.3, funcsigs-1.0.2, mock-2.0.0, paramiko-2.4.0,
        pbr-3.1.1, pip-9.0.1, pycrypto-2.6.1, pytest-3.4.1, python-dateutil-2.6.1,
        pytz-2018.3, setuptools-38.4.0, six-1.11.0, virtualenv-15.1.0

*Here you can see that module spider can list additional information about a specific version of a module. In this case it lists the home page of the module if one exists, as well as the included python packages (In this example)*

*NB. The above example with python does not list all available python packages. Some python packages such as mpi4py and numpy are their own modules on OzSTAR*
