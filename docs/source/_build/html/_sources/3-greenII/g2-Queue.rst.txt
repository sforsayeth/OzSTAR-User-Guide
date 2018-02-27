.. highlight:: rst

Job Queues on Green II
==========================================

The preferred method for utilising the Green II supercomputer is through the job queue which accesses the majority of the compute nodes (all except the interactive nodes). The queue nodes are not available for direct login.

The basic goal of a queue system is to maximize utilisation of the supercomputer and to do this in a way that is fair to all users. It should also ease the workload for users who do a lot of computation. Our queue system is based on PBS. It employs a queue manager called Torque and a scheduler system called Moab. `Torque <http://www.adaptivecomputing.com/products/open-source/torque/>`_ manages the resources of the supercomputer and monitors what is happenning on the nodes while `Moab <http://www.adaptivecomputing.com/products/hpc-products/moab-hpc-basic-edition/>`_ takes care of job scheduling. The latter is done in a fair manner so that those who have only used the supercomputer sparingly will find it easier to start a job than those who have used the cluster heavily. However, if resources are available and the scheduler has a job that can use those resources, then that job will be started regardless of the prior usage history of the user.

All jobs are to be submitted from the head-node ``g2.hpc.swin.edu.au`` using the ``qsub`` command. When your job is submitted it will be assigned a jobid.

**Do not run processes directly on the g2 headnode**. Instead use an **interactive node** (see the Hardware section) or launch an **interactive queue session** (see below). *Note: Processes that run on the g2 headnode will be automatically killed if they exceed defined cpu or memory limits.*

Available queues
------------------

Queue Name: gstar
^^^^^^^^^^^^^^^^^^^^^^
- **Nodes:** gstar011 through to gstar058
- **Node Hardware Specifications:** 12 cores, 48GB RAM, 2xC2070 GPUs per node
- **Per User Resource Limits:** processors = 192, walltime = 7 days
- **User Restrictions:** none

Queue Name: manygpu
^^^^^^^^^^^^^^^^^^^^^^
- **Nodes:** gstar101, gstar102, gstar103
- **Node Hardware Specifications:** 12 cores, 48GB RAM, 7xM2090 GPUs per node
- **Per User Resource Limits:** nodes = 1, walltime = 7 days
- **User Restrictions:** none

Queue Name: sstar
^^^^^^^^^^^^^^^^^^^^^^
- **Nodes:** sstar011 to sstar030 and sstar101 to sstar162
- **Node Hardware Specifications:** 16 cores, 64GB RAM, K10 GPUs in nodes sstar101-162
- **Per User Resource Limits:** processors = 256, walltime = 7 days
- **User Restrictions:** none (SUT prioritised)

*notes: include* **gpus=1** *or* **gpus=2** *in your resource list if you need a K10 node*

Queue Name: largemem
^^^^^^^^^^^^^^^^^^^^^^
- **Nodes:** sstar201, sstar202, sstar203, sstar204
- **Node Hardware Specifications:** 32 cores, 512GB RAM per node (no GPUs)
- **Resource Limits:** processors = 64, walltime = 2 days
- **User Restrictions:** SUT only

*Notes: previously accessed through the sstar queue by including largemem in your resource list but now these nodes have a dedicated queue.*


Queue limits
-------------

As well as the resource limits described above, there are also limits within Moab on the number of jobs that you can have within the queue and how these are managed. Currently the maximum number of jobs that you can have submitted at any point in time is 1000 (generally your instantaneous usage will be much less than this).

Jobs will be classified as running (or active), idle (or eligible) or blocked. Idle jobs are next in line to run when resources become available. Currently we have a limit of 100 set on the number of idle jobs (in total for all users per queue) and when this becomes full jobs will then go into the blocked state. As idle jobs become active, jobs from the blocked state will move up to idle and so on.

The maximum number of active jobs you can have at any one time is currently set at 64. The number of GPUs in use by one person is also set to 64.
Note that if you are using the maximum number of GPUs then subsequent queued jobs will go into the blocked state but will be promoted when your GPU use count drops below the maximum.