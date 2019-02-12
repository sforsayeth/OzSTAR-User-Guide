.. highlight:: rst

Job Queues
==================================

The preferred method for utilising the supercomputer is through submitting batch jobs to the Slurm scheduling system which governs accesses to the compute nodes. The queue nodes are not available for direct login.

The basic goal of a queue system is to maximize utilisation of the supercomputer and to do this in a way that is fair to all users. It should also ease the workload for users who do a lot of computation. The queue system on OzSTAR is `Slurm <https://slurm.schedmd.com>`__. Slurm is a resource manager, job scheduler, and much more. Slurm was first developed at the `Livermore Computing Center <https://hpc.llnl.gov/>`__, and has grown into a complete open-source software backed up by a large community, commercially supported by the original developers, and used by many of the Top500 supercomputers. It is therefore worth learning its mechanisms.

Key Slurm Information
---------------------

Slurm on OzStar splits nodes into partitions (which can overlap) in order to give prioritisation and classification of nodes. Slurm will direct most jobs correctly to the requested partition, but the Knights Landing nodes will need attention by the user.

Available Partitions
--------------------
Slurm's *partitions* are comparable to PBS's *queues* (e.g. :doc:`torque-vs-slurm`). Currently available partitions are:

- ``skylake`` for non-GPU jobs on Skylake CPU nodes
- ``skylake-gpu`` for GPU jobs only on Skylake CPU nodes with nVidia p100/v100 GPUs
- ``knl`` for Intel Xeon Phi Knights Landing (KNL) nodes
- ``sstar`` for GPU and non-GPU jobs on older SandyBridge CPUs with nVidia k10/k80 GPUs

With the exception of ``knl``, you should not specify any partition. From the ``farnarkle1/2`` login nodes, the ``skylake`` partition is the default, and gpu jobs are automatically redirected to ``skylake-gpu``. When logged into the ``sstar`` login node, the ``sstar`` partition is the default.

Most people should use the ``skylake`` partition. If ``skylake`` is busy then ``sstar`` is usually an easy alternative and these older cpus probably aren't as slow as you think. ``knl`` nodes have many cores but usually require very large scale threading and AVX2 to get good performance.

Note: Because the ``skylake``, ``knl`` and ``sstar`` partitions all have different CPU architectures, and :doc:`Modules` may also differ slightly, codes almost always require a rebuild to use different partitions.

Slurm Options
-------------
Options to Slurm can be specified on the ``sbatch`` or ``srun`` command lines like ``sbatch --time=1:00:00 ...`` or in lines at the top of the batch script as ``#SBATCH --time=1:00:00``. In the below we mostly use the command line versions for brevity, but typically these options will all be written into the top of your batch script. Your batch script is then submitted to Slurm with ``sbatch my_script``.

Memory Requests
---------------
On OzStar you must request the amount of memory that your job needs.  The default allocation is ``100MB`` per CPU core requested which is unlikely to be enough to achieve much and is intended to encourage you to pick a good value.  The more accurate your estimate can be the more likely your job is to be scheduled quickly as Slurm will be better able to fill up available slots in its schedule with it. Note that you should only request the amount of memory that you are going to use. Requesting more will stop other peoples jobs from running.

For instance if your job needs 2GB per CPU core then you would ask for ``--mem-per-cpu=2G``.  If your job needs needs around 1.5GB you could ask for ``--mem-per-cpu=1500M``.

The maximum memory request for the vast majority of nodes (``John`` in ``skylake``) is one of ``--mem=186g``, ``--mem=191000`` (MB), ``--mem-per-cpu=5G``, ``--mem-per-cpu=5968`` (MB). If you ask for more memory than this then your job will be automatically redirected one of the ``Bryan`` nodes which have more RAM available. However there are only few high memory nodes so your job throughput will be low. Again, do not request more than you need as it will also stop other people's jobs from running.

Almost all ``sstar`` nodes physically have ``--mem=62g``, which per-cpu is ``--mem-per-cpu=4000M``.

Slurm enforces this memory request by using the Linux kernels ``cgroup`` support which will limit the memory it can use on the node. If your job exceeds that value then the kernel will kill a process which will usually lead to the failure of your job.

Requesting GPUs
---------------
If your application uses GPUs then you will need to request them to be able to access them.  To request a GPU you need to ask for ``generic resources`` by doing ``--gres=gpu``.  You can request 2 GPUs on a node with ``gres=gpu:2``.  This will request will ensure that for each node you are allocated you will have sole access to that number of GPUs.  Access is controlled via the Linux kernel ``cgroup`` mechanism which locks out any other processes from accessing your allocated GPU.

Note that whilst we reserve 4 CPUs per node for GPU tasks we cannot currently guarantee they have optimal locality and so you may find performance varies across jobs depending on which core and which GPU you are allocated.

Interactive Jobs - Running a program on a compute node but with output on the login node
-----------------------------------------------------------------------------------------

If you need to run a program on a compute node that will ask questions, or you would like to watch its output in real time, then you can use the ``srun`` command to achieve this, in the same way you would use it to launch an MPI program from within a Slurm batch script.  To run an MPI program interactively you could do:

	``srun --time=4:0:0 --cpus-per-task=4 --ntasks=4 --mem-per-cpu=2G ./my-interactive-mpi-program``

You would then have to wait until the job started and then be able to interact with it as if you were running it on the login node.

Note: Because ``skylake`` and ``sstar`` partitons have different CPU architectures, srun/sinteractive must be invoked from a login node with a matching architecture. ie. Use ``farnarkle1/2`` for ``skylake``, and the ``sstar`` login node for the ``sstar`` partition.

Interactive Jobs - Getting a shell prompt on a compute node
-----------------------------------------------------------
OzStar has no dedicated interactive nodes, instead you can request them using the ``sinteractive`` command which will give you a shell on a compute node as part of a job.  It takes all the usual options that the Slurm ``srun`` command takes to allow you to specify the run time of your job, how much memory it needs and how many cores it needs on the node. Again you will need to wait until the job this generates starts before being able to do anything.

	``sinteractive --time=1:0:0 --mem=4g --cpus-per-task=4``

Note: Because ``skylake`` and ``sstar`` partitons have different CPU architectures, srun/sinteractive must be invoked from a login node with a matching architecture. ie. Use ``farnarkle1/2`` for ``skylake``, and the ``sstar`` login node for the ``sstar`` partition.

Interactive Jobs - Using X11 applications
-----------------------------------------
In both the above examples you can pass the ``--x11`` option to ``srun`` or ``sinteractive`` to request X11 forwarding.  Please note that this will not work if you try and run this inside of ``screen`` or ``tmux``!

Requesting Local Scratch Space
------------------------------
All jobs on OzStar get allocated their own private area on local disk which is pointed to by the environment variable ``$JOBFS``. These are cleaned up at the end of every job.  By default you get a ``100MB`` allocation of space, to request more you need to ask for it with the ``--tmp`` option to ``sbatch``, so for example to request 4GB of local scratch disk space you would use ``--tmp=4G``.
