.. highlight:: rst

Profiling your code
=====================

Using Valgrind
---------------

`Valgrind <http://valgrind.org/>`_ is a tool suite that provides a number of debugging and profiling tools. It is commonly used to check for or debug memory allocation bugs. Using Valgrind does not require program recompilation, however it is recommended that the application be compiled with -g so that additional symbol information be used. It should be noted that running Valgrind can be significantly slower than a normal run, so using a smaller test cases is recommended for profiling.

There are three different compilations of Valgrind on green II : ``valgrind/x86_64``, ``valgrind/x86_64/gnu``, and ``valgrind/x86_64/gnu/3.7.0``. You can load the default version using:
::

    module load valgrind

To use Valgrind to check your code:
::

    valgrind --tool=[valgrindTool] [myexecutable]

The default Valgrind tool is : ``memcheck``

If you want to use valgrind for an MPI application use:
::

    mpirun -np [Number of Processes] valgrind –-tool=[valgrindTool] myexecutable

You can find more details on : `Valgrind Quick Start Guide <http://valgrind.org/docs/manual/quick-start.html>`_ or `Valgrind User Manual <http://valgrind.org/docs/manual/manual.html>`_

Using nvprof
-------------

The `nvprof <http://docs.nvidia.com/cuda/profiler-users-guide/#nvprof-overview>`_ profiling tool enables you to collect and view CUDA profiling data from the command-line. ``nvprof`` enables the collection of a timeline of CUDA-related activities on both CPU and GPU, including kernel execution, memory transfers, memory set and CUDA API calls.
You can call ``nvporf`` using:
::

    nvprof mycuda_exec

If multiple CUDA capable devices are profiled, use
::

    nvprof --print-summary-per-gpu

to print one summary per GPU.

An alternative option is to use the `Command Line Profiler <http://docs.nvidia.com/cuda/profiler-users-guide/index.html#compute-command-line-profiler-overview>`_.

Please note that ``nvprof`` and the ``Command Line Profiler`` are mutually exclusive profiling tools. If ``nvprof`` is invoked when the command-line profiler is enabled, ``nvprof`` will report an error and exit.

Monitoring Your Resource Usage
-------------------------------

If you want to see the resource usage of your code while running in batch mode you can add the following calls to your PBS script:
::

    top -d [Number of Update] -s [time] -b > MyCodeUsage.txt &

Where

- ``[time]`` is the delay between screen updates to time seconds. The default delay between updates is 5 seconds.
- ``[Number of Update]`` shows only this number of displays, then exit. A display is considered to be one update of the screen. This option allows you to select the number of displays you wants to see before top auto-matically exits. is the number of screens updates before top exits.
- ``-b`` is used to select “batch” mode. In this mode, all input from the terminal is ignored.

You can use nvidia-smi to do the same thing with GPUs.
::

    nvidia-smi --loop=30 -a  > nvidiasmioutput.txt &

You will need to kill the nvidia-sim process after your code finished because it loops indefinitely until ctrl-c is triggered.. You can use
::

    pkill nvidia-smi