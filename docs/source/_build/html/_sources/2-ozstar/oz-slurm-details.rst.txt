.. highlight:: rst

Slurm: gathering information
==================================

Slurm includes many commands to interact with the system. For example, the ``sinfo`` command provides an overview of the resources offered by the cluster, and the ``squeue`` command shows to which jobs those resources are currently allocated.

sinfo
---------

By default, ``sinfo`` lists the partitions that are available. A partition is a set of logically-grouped compute nodes.

::

    % sinfo
    PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
    skylake*     up 7-00:00:00      2  alloc john[5,99]
    skylake*     up 7-00:00:00    113   idle bryan[1-8],john[1-4,6-98,100-107]
    knl        down 1-00:00:00      4   idle gina[1-4]
    debug        up 1-00:00:00    117   idle bryan[1-8],gina[1-4],john[1-4,6-98,100-107], john[5,99]

In the above example, we see three partitions, named skylake, knl, and debug. The partition marked with an asterisk is the default partition (e.g. skylake). All nodes of the debug partition are idle, along with some of the knl and skylake partitions, while two of the skylake partition are being used.

.. note::

    If the output of the ``sinfo`` command is organised differently from the above, it probably means default options are set through environment variables. Use ``printenv | grep ^SINFO`` to review them.

The command ``sinfo`` can output the information in a node-oriented fashion, with the argument ``-N``
::

    % sinfo -N -l
    Thu Jan 25 15:43:52 2018
    NODELIST   NODES PARTITION       STATE CPUS    S:C:T MEMORY TMP_DISK WEIGHT AVAIL_FE REASON
    bryan1         1  skylake*        idle   36   2:18:1 384000        0   1000   (null) none
    bryan1         1     debug        idle   36   2:18:1 384000        0   1000   (null) none
    bryan2         1  skylake*        idle   36   2:18:1 384000        0   1000   (null) none
    bryan2         1     debug        idle   36   2:18:1 384000        0   1000   (null) none
    (...)
    john106        1  skylake*        idle   36   2:18:1 191000        0      1   (null) none
    john106        1     debug        idle   36   2:18:1 191000        0      1   (null) none
    john107        1  skylake*        idle   36   2:18:1 191000        0      1   (null) none
    john107        1     debug        idle   36   2:18:1 191000        0      1   (null) none

.. note::

    The ``-l`` argument allow to print more information about the nodes: number of CPUs (CPUs), Memory, temporary disk (also called scratch space; TMP_DISK), node weight (internal parameter specifying preferences in nodes for allocations when there are multiple possibilities; WEIGHT), features of the nodes (e.g. processor type; AVAIL_FE), reason why a node is down, if applicable (REASON).

It is possibly to specify precisely what information to output with ``sinfo`` by using its ``--format`` argument. More information can be found by looking at the command manual page via ``man sinfo``.

squeue
---------

The ``squeue`` command shows the list of jobs which are currently running (they are in the *RUNNING* **state**, noted as ‘R’) or waiting for resources (noted as ‘PD’, short for *PENDING*).

::

    % squeue
    JOBID PARTITION NAME USER ST  TIME  NODES NODELIST(REASON)
    12345     debug job1 dave  R   0:21     4 gina[1-4]
    12346     debug job2 dave PD   0:00     8 (Resources)
    12348     debug job3 ed   PD   0:00     4 (Priority)

The above output show that is one job running, whose name is job1 and whose jobid is 12345. The jobid is a unique identifier that is used by many Slurm commands when actions must be taken about one particular job. For instance, to cancel job job1, you would use ``scancel 12345``. Time is the time the job has been running until now. Node is the number of nodes which are allocated to the job, while the ``Nodelist`` column lists the nodes which have been allocated for running jobs. For pending jobs, that column gives the reason why the job is pending. In the example, job 12346 is pending because requested resources (CPUs, or other) are not available in sufficient amounts, while job 12348 is waiting for job 12346, whose priority is higher, to run. Note that the priority for pending jobs can be obtained with the ``sprio`` command.

There are many options you can use to filter the output by:

 - **user**: ``--user``
 - **partition**: ``--partition``
 - **state**: ``--state``
 - etc.

Here again, ``sprio``'s output can be customized with the ``--format`` parameter.
