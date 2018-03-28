.. highlight:: rst

What's new on OzSTAR?
==========================

Access
-------

Entry point is now ``ozstar.swin.edu.au``.

Hardware
----------

Each node includes 36 CPU cores (4 of which are reserved solely for GPU jobs) and 2 P100 GPUs. Interconnect is 100 Gb/s Intel infiniband. `See here for more details <https://supercomputing.swin.edu.au/ozstar/>`_.

Interactive Nodes
-------------------

There are no dedicated interactive nodes on OzSTAR. However, you can compile and run test jobs on the two login
nodes or start an interactive session via Slurm.

Job Scheduling
----------------

We now use Slurm on OzSTAR. See :doc:`../2-ozstar/torque-vs-slurm` for a comparision between Torque/Moab and Slurm.

Memory Requests
----------------

Job memory limits are enforced by Slurm. The default is 100MB of RAM. If you require more you can request it with eg. ``--mem=20g`` or ``--mem-per-cpu=2g``, up to the per-node limit (on the majority of nodes) of ``--mem=186g`` or ``--mem=191000`` (MB). Do not request more than you need as it will stop other people's jobs from running.

Large memory nodes
---------------------

Standard nodes on OzSTAR have 192 GB of RAM. There are eight nodes with increased memory: four with 384 GB RAM, and four with 768 GB RAM. Each of these also has a 2 TB NVMe flash SSD drive available.

Projects
------------

You will be a member of a new project on OzSTAR as g2 projects are not rolled over. Currently, the prefered way to copy
files and repositories from g2 to OzSTAR is with ``rsync``. For example, while logged on OzSTAR, you can bring files over via
``rsync -av g2:/some/file .``.


File System
--------------

The main filesystem is again Lustre-based. Your root project directory on lustre is now ``/fred/<project_id>``.
Home directory path is unchanged.
The filesystem automatically and transparently compresses all your files to save diskspace and improve data throughput.

Modules
-----------

OzSTAR now includes generic and optimized versions of modules. See :doc:`../2-ozstar/Modules` for more information.
