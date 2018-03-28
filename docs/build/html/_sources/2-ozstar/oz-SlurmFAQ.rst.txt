.. highlight:: rst

Slurm: FAQ
============================

How do I submit a job to a specific queue?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Slurm uses the term partition rather than queue. To submit a job to a given partition, use the ``--partition`` option of the ``sbatch``, ``srun``, or ``salloc`` commands.

Queues can also appear under the form of qualities of service (QoS). To use a particular QoS, use the ``--qos`` option of the above-listed commands.

To view all partitions on a cluster, use sinfo, while qualities of service can be listed with ``sacctmgr list qos``.

Is OpenMP ‘slurm-aware’?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
No, you need to set ``export OMP_NUM_THREADS=...`` in your submission script.

For instance, if you requested several cores with the ``--cpus-per-task`` option, you can write:
::

    export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

Is MPI ‘slurm-aware’ ?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Yes, you do not need to specify the -np nor the -host, or hostfile options to mpirun or mpiexec. Simply go with
::

    mpirun ./a.out

assuming you requested several cores with ``--ntasks``

Do not forget to set the environment correctly with something like ``module load gcc/6.4.0 openmpi/3.0.0`` if necessary.

How do I know how much memory my job is using or has used ?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If your job is still running, you can have memory information with ``sstat``. If your job is done, the information is provided by ``sacct``. Both support the ``--format`` option so you can run, for instance:

::

    sacct --format JobID,jobname,NTasks,nodelist,MaxRSS,MaxVMSize,AveRSS,AveVMSize

See the manpages for both utilities:

::

    man sstat
    man sacct

When will my job start ?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
A job starts either when it has the highest priority and the required resources are available, or when it has an opportunity to backfill. The ``squeue --start`` command gives an estimation of the date and time a job is supposed to start but beware that the estimation is based on the situation at a given time. Slurm cannot anticipate higher-priority jobs being submitted after yours, or machine downtimes which lead to fewer resources for the jobs, or job crashes which can lead to large jobs starting earlier than expected thus making smaller jobs scheduled for backfilling to lose that backfilling opportunity.

How to pick a partition to start a job as early as possible?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Simply submit the job to all the partitions you are considering, by listing them with the ``--partition`` option:

::

    #SBATCH --partition=partition1,partition2

The job will be submitted to the partition which offers the earliest allocation according to your job parameters and priority.


.. How do I use the local scratch space ?
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    Slurm offers the sbcast command that propagates a file to the local file systems of the nodes that were allocated to the job. However, ``sbcast`` works one file at a time. It is therefore unsuited for copying entire data directories.

    One neat way to deal with several files is to use the ``srun cp`` construction.

    For instance, in the script below:
    .. ::

        #!/bin/bash
        #SBATCH --nodes=2
        #SBATCH --output=output.txt
        SCRATCH=$LOCALSCRATCH/$SLURM_JOB_ID

        echo Creating temp dir $SCRATCH
        srun mkdir -p $SCRATCH || exit $?
        echo Copying files. srun cp is equivalent to loop over each node + scp
        srun cp -r $SLURM_SUBMIT_DIR/*  $SCRATCH || exit $?

    a temporary SCRATCH directory is created with the jobid. Afterwards, the data is copied from the home to the local scratch (assuming the home directory is mounted on each compute node).

    The environment variables used in the script provided by Slurm are:

    - ``SLURM_JOB_ID``: jobid
    - ``SLURM_SUBMIT_DIR``: the directory you were when you launched the script with sbatch

    The variable ``$LOCALSCRATCH`` is provided by the CECI environment (see Local scratch).

    If each output file had a different name from the input ones, we could simply ``srun cp`` from the scratch to the home after the code run.
    .. ::

        echo Copying back output files to home submit directory
        srun cp -r  $SCRATCH/* $SLURM_SUBMIT_DIR/*  || exit $?

    At the end, make sure to clean the scratch space.
    .. ::

        echo Removing $SCRATCH
        srun rm -rf $SCRATCH || exit $?

.. How do I get the node list in full rather than in compressed format ?
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    Slurm describes node lists with notations like hmem[05-07,09-17]. To get the full list, use the ``scontrol`` command:
    ::

        dfr@hmem00:~ $ scontrol show hostname hmem[05-07,09-17] | paste -d, -s
        hmem05,hmem06,hmem07,hmem09,hmem10,hmem11,hmem12,hmem13,hmem14,hmem15,hmem16,hmem17

.. How do I know which slots exactly are assigned to my job ?
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    The command ``scontrol show -d job jobid`` gives very detailed information about jobs.

.. How do I create a parallel environment?
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    Slurm ignores the concept of parallel environment as such. Instead, it simply requires that the number of nodes, or number of cores be specified. However, you can have control on how cores are allocated (e.h. on a single node, on several nodes, etc.) using the ``--cpus-per-task`` and ``--ntasks-per-node`` options for instance.

    With these options, there are several ways to get the same allocation. For instance, the following : ``--nodes=4 --ntasks=4 --cpus-per-task=4`` is equivalent in terms of resource allocation to ``--ntasks=16 --ntasks-per-node=4``. However, each one will lead to environment variables being set, and understood, differently by ``srun`` and ``mpirun``. In the first case 4 processes are launched, while in the second case 16 processes will be launched.

    Suppose you need 16 cores, these are possible scenarios:

    - you use mpi and do not care about where those cores are distributed: ``--ntasks=16``
    - you want to launch 16 independent processes (no communication): ``--ntasks=16``
    - you want those cores to spread across distinct nodes: ``--ntasks=16 --ntasks-per-node=1`` or ``--ntasks=16 --nodes=16``
    - you want those cores to spread across distinct nodes and no interference from other jobs: ``--ntasks=16 --nodes=16 --exclusive``
    - you want 16 processes to spread across 8 nodes to have two processes per node: ``--ntasks=16 --ntasks-per-node=2``
    - you want 16 processes to stay on the same node: ``--ntasks=16 --ntasks-per-node=16``
    - you want one process that can use 16 cores for multithreading: ``--ntasks=1 --cpus-per-task=16``
    - you want 4 processes that can use 4 cores each for multithreading: ``--ntasks=4 --cpus-per-task=4``

.. How do I cancel a job?
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    Use the ``scancel jobid`` command with the jobid of the job you want cancelled. In the case you want to cancel all your jobs, type ``scancel -u login``. You can also cancel all your pending jobs for instance with ``scancel -t PD``.

.. How can I get my job to start early?
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    First, make sure you only request the resources you need. The more you ask, the longer you will wait. Then, try and make your job flexible in terms of resources. If your program is able to work through the network, do not ask for all tasks on the same node. Use the Slurm options cleverly. For instance, the ``--nnodes`` option allows specifying a range of number of nodes, e.g. ``--nnodes=2-4``, meaning that your job will start as soon as at least two nodes are available, but if, by then, four nodes are available, you will be allocated four nodes. Another example is ``–time-min``, which allows you to specify a minimum running time which you are willing to set for your job if it makes it possible to start it earlier (through backfilling). Note that you can get, at any moment, the remaining time for your job in your script with the ``squeue -h -j $SLURM\_JOBID -o %L`` and that the ``--signal`` option can be used to be warned before the job is killed.

    Of course, it is always easier to get a job running when there are few jobs waiting in the queue. Try to plan ahead your work and submit your jobs when people usually do not work on the cluster: holidays, exam periods, etc.

.. How do I choose a node with certain features (e.g. CPU, GPU, etc.) ?
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    Slurm associates to each node a set of Features and a set of Generic resources. Features are immutable characteristics of the node (e.g. network connection type) while generic resources are consumable resources, meaning that as users reserve them, they become unavailable for the others (e.g. compute accelerators).

    Features are requested with ``--constraint="feature1&feature2"`` or ``--constraint="feature1|feature2"``. The former requests both [using the *logical and* (``&``)], while the latter requests at least one of ``feature1`` or ``feature2`` [using the *logical or* (``|``)]. More complex expressions can be constructed. Type ``man sbatch`` for details.

    Generic resources are requested with ``--gres="resource:2"`` to request 2 resources.

    Generic resources and features can be listed with the command scontrol show nodes.

.. How do I get the list of features and resources of each node ?
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    The command sinfo gives such information. You need to run it with specific output parameters though:
    ::

        sinfo -o "%15N %10c %10m  %25f %10G"

    It will output something like:
    ::

        dfr@manneback:~ $ sinfo -o "%15N %10c %10m  %25f %10G"
        NODELIST        CPUS       MEMORY      FEATURES                  GRES
        mback[01-02]    8          31860+      Opteron,875,InfiniBand    (null)
        mback[03-04]    4          31482+      Opteron,852,InfiniBand    (null)
        mback05         8          64559       Opteron,2356              (null)
        mback06         16         64052       Opteron,885               (null)
        mback07         8          24150       Xeon,X5550                TeslaC1060
        mback[08-19]    8          24151       Xeon,L5520,InfiniBand     (null)
        mback[20-32,34] 8          16077       Xeon,L5420                (null)

    In the above screen capture, we see that some compute nodes have Infiniband connections, some have Intel processors, while others have AMD processors. The node mback07 furthermore has one GPU, which is a generic resource in the sense that once requested by a job, it becomes unavailable for the others.

