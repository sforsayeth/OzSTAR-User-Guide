.. highlight:: rst

Job Queues
==================================

The preferred method for utilising the supercomputer is through the job queue which accesses the majority of the compute nodes (all except the interactive nodes). The queue nodes are not available for direct login.

The basic goal of a queue system is to maximize utilisation of the supercomputer and to do this in a way that is fair to all users. It should also ease the workload for users who do a lot of computation. The queue system on OzSTAR is based on `Slurm <https://slurm.schedmd.com>`__. Slurm is a resource manager, job scheduler, and much more. Slurm was first developed at the Livermore Computing Center, and has grown into a complete open-source software backed up by a large community, commercially supported by the original developers, and used by many of the Top500 supercomputers. It is therefore worth learning its mechanisms.

Available Partitions
-----------------------------------------------
Slurm's *partitions* are comparable to Moab's *queues* (e.g. :doc:`torque-vs-slurm`). Currently available partitions
include ``skylake`` (default nodes), ``skylake-gpu`` (default nodes including GPUs), and ``knl`` (Intel Xeon Phi KNL nodes).

.. Partition Name: skylake
    ^^^^^^^^^^^^^^^^^^^^^^^^^^
    *More information to come.*

    Partition Name: knl
    ^^^^^^^^^^^^^^^^^^^^^^^^^^
    *More information to come.*

    Partition Name: largemem
    ^^^^^^^^^^^^^^^^^^^^^^^^^^
    *More information to come.*

    Queue limits
    -----------------------------------------------
    *More information to come.*

