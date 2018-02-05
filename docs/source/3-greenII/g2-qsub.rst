.. highlight:: rst

Submitting Jobs on Green II (qsub)
===================================

Green II uses a batch server referred to as the cluster management server, which is running on the g2 head node. This batch server monitors the status of the supercomputer and controls/monitors the available queues and the currently active jobs. `qsub <http://docs.adaptivecomputing.com/torque/4-1-4/Content/topics/commands/qsub.htm>`_ is the how you can interact with the batch server. It takes several command line arguments and can also use special directives in the submission scripts. You can have more details about ``qsub`` by:
::

    man qsub

There are two modes of using the qsub command: **Interactive Job Submission**, and **PBS Script Jobs**.

In both of these modes, you have to specify which queue resources will be used for your job (using -q parameter). You can select one of the following queues (for more details about each queue resources and limitations see job queue):

- gstar
- manygpu
- sstar
- largemem

Please note that some of these queues are not available for non-Swinburne users.

**Other useful Commands:**

- ``qdel``: Delete a batch job
- ``qstat``: Show status of all jobs (The time displayed is the CPU time used by the job)
    * ``qstat -s``: Show status of all jobs (The time displayed is the walltime used by the job)
    * ``qstat -u [userid]``: Shows the status all PBS jobs submitted by the user [userid] (The time displayed is the walltime used by the job).
    * ``qstat -n``: Shows the status all PBS jobs along with a list of compute nodes that the job is running on.
    * ``qstat -f [jobid]``: Shows detailed information about the job [jobid].
- ``qps``: Show processes (or optionally threads) currently running in your jobs.
- ``qcat``: Show output (stdout, or optionally stderr) from your currently running jobs.
- ``showq``: Displays information about active, eligible, blocked, and/or recently completed jobs. For more information about ``showq``, see `this page <http://docs.adaptivecomputing.com/maui/commands/showq.php>`__.
- ``showstart``: displays the estimated start time of a job. Please note that this is an estimation that might changes depending on different run-time factors. For more information about ``showstart``, click `this page <http://docs.adaptivecomputing.com/maui/commands/showstart.php>`__.
- ``checkjob``:displays detailed job state information and diagnostic output for a specified job. For more information about ``checkjob``, see `this page  <http://docs.adaptivecomputing.com/maui/commands/checkjob.php>`__.

Submitting Interactive Jobs
----------------------------
::

    qsub -q [Queue Name] -A [Your Project ID] -I -l [Resources Required]


- ``-q [Queue Name]``: parameter is used to set the queue you want to get an interactive node from.
- ``-A [Your Project ID]``: parameter is used to deduct this usage from a specific project (e.g p001_swin). You must be a member of that group.
- ``-l [Resources Required]``: parameter is used to set the resources requested from the queue. (e.g walltime=1:0:0,nodes=1:ppn=1:gpus=1,mem=2000MB - See `Specifying PBS Resources <https://supercomputing.swin.edu.au/user-guide/submitting-jobs/#PBS_Resources>`_ for more details.)
- ``-I``: parameter is used to specify that you want an interactive job.


The session will be active as long as your session remains connected. When you wish to terminate the job simply type ``exit``.


Running PBS Script Job
----------------------------

A PBS script file is a standard Linux shell script file that contains a few extra comments at the beginning that specify directives to PBS to describe the resources needed, and which queue do you want to use. These comments all begin with #PBS. The most important PBS directives are:


PBS Directive
^^^^^^^^^^^^^^

- ``#PBS -l walltime=HH:MM:SS``:
    * This directive specifies the maximum walltime (real time) that a job should take. If this limit is exceeded, PBS will stop the job. Keeping this limit close to the actual expected time of a job can allow a job to start more quickly.
- ``#PBS -l pmem=SIZEgb``:
    * This directive specifies the maximum amount of physical memory used by any process in the job. For example, if the job would run four processes and each would use up to 4 GB (gigabytes) of memory, then the directive would read #PBS -l pmem=4gb and this mean the maximum job memory requirements is 16GB.

- ``#PBS -l nodes=N:ppn=M``:
    * This directive specifies the number of nodes (nodes=N) and the number of processors per node (ppn=M) that the job should use.
- ``#PBS -q queuename``:
    * This directive specifies the PBS queue this job should be submitted to.                                                                                                                                      - ``#PBS -N Job_name``:
    * This directive specifies the Job Name.
- ``#PBS -A Project_ID``:
    * If you are a member of multiple projects, This directive specifies the Project ID.

A PBS Script file can be submitted using:
::

    qsub scriptfile

The ``qsub`` command will return a job ID. It should in the form of ``[job Number].pbs.hpc.swin.edu.au``. This ID can be used later to control/monitor the job.

Viewing Job Output
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

By default PBS will write screen output from a job to the follwing files:

- ``[Jobname].o[Job_ID]``: This file would contain the non-error output.
- ``[Jobname].e[Job_ID]``: This file would contain the error output.

If the PBS directive ``#PBS -j oe`` is used in a PBS script, the non-error and the error output are both written to the Jobname.oJob_ID file.

Sample PBS Script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
::

    #!/bin/csh
    # specify the queue name
    #PBS -q gstar
    # resource requests
    #PBS -l nodes=1:ppn=6:gpus=1
    #PBS -l walltime=01:00:00:00

    # list the assigned CPUs and GPUs
    echo Deploying job to CPUs ...
    cat $PBS_NODEFILE
    echo and using GPU ...
    cat $PBS_GPUFILE

    # run process
    ./a.out < inputX
    PBS Environment Variables

The following environment variables will be provided for your job via the PBS system.

- ``$PBS_O_WORKDIR``: absolute path of the current working directory.
- ``$PBS_ENVIRONMENT``: set to PBS_BATCH to indicate the job is a batch job, or to PBS_INTERACTIVE to indicate the job is a PBS interactive job
- ``$PBS_JOBID``: the job ID assigned to the job.
- ``$PBS_JOBNAME``: the job name supplied by your script. Check -N option.
- ``$PBS_NODEFILE``: the path of the file contains the list of nodes assigned to the job
- ``$PBS_QUEUE``: the name of the queue used to execute the job.
- ``$PBS_GPUFILE``: the path of the file contains the list of GPU .assigned to the job.

Specifying PBS Resources
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To get a summary of the resources supported by PBS for gSTAR use:
::

    man pbs_resources

These resources are specified by including them in the -l option argument on the qsub command or in the PBS job script. As a specialized resource you can ask for gpus per node by using “gpus=” as one of the resources. If you did not explicitly ask for GPU, the scheduler will assume that you don’t need one.

GPU device locking
-------------------

To avoid possible conflicts when multiple users are using the same node, we currently have a policy where a GPU is locked to a user when assigned to that user through the job queue. This is done by temporarily changing the permissions of the device to give ownership to the user. However, this does present a known issue when using the ``nvidia-sim -a`` command to perform device queries. For example, if person A is already using GPU-0 on the node (and thus has ownership of it) then when another person queries the available devices it will only detect one device (GPU-1) and will refer to it as device 0. In such cases it is advisable to use other methods to pick up the allocated device number (such as using the PBS_GPUFILE in the example below).