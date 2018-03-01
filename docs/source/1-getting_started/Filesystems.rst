.. highlight:: rst

File systems
=============

Directories
-------------

On OzSTAR, the two main directories are: ::

    /home/<username>

and ::

    /fred/<project_id>

noting that ``/home`` is for personal access and ``/fred`` is for project access.

Both ``/home`` and ``/fred`` directores are using the Lustre filesystem. The ``/home`` directory is backed up. It has a 10GB quota, and 100,000 files. The ``/fred`` directory is where all of your project data should be stored. There is **NO backup for** ``/fred`` **and the user must take responsibility for backing up any data that is important (somewhere other than OzSTAR).**

You are free to create new repositories inside ``/home/<username>`` (e.g. ``/home/<username>/my_code``). Similarly, you can create directories for project use inside ``/fred/<project_id>`` (e.g. ``/fred/<project_id>/my_project_code/``).

Lustre
------

OzSTAR includes ~5 Petabytes of Lustre-zfs Filesystem for the nodes. The achievable bandwidth into each OSS (Object Storage Server) is approximately 30 Gb/s.

Lustre manuals are at `Intel HPDD Wiki <https://wiki.hpdd.intel.com/display/PUB/Documentation>`_

Dealing with Lustre
-------------------

Quota
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Quotas have been enabled on OzSTAR.

The default quota is 10TB for projects, and 100,000*10 files for projects. If you need extra storage, please contact hpc-support@swin.edu.au

To check your quota:

``lfs quota /fred``

Output:
::

    [username@pbs ~]$ lfs quota /fred
    Disk quotas for usr username (uid 1000):
         Filesystem    kbytes    quota   limit   grace   files   quota   limit   grace
              /fred [1041408]  5242880 6291456       -     [0]       0       0       -

.. There is also a helpful command for checking the usage of all projects you are associated with. Just type ``user-quota``
(this needs to be done on the head node).

.. Free space
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

    The lfs df command shows available disk space on the mounted Lustre file system and space consumption per OST. If multiple Lustre file systems are mounted, a path may be specified, but is not required.

    ::

        root@pbs [~] ->lfs df -h
        UUID                       bytes        Used   Available Use% Mounted on
        lustre-MDT0000_UUID         1.0T        7.4G      968.6G   1% /lustre[MDT:0]
        lustre-OST0000_UUID        14.5T      295.4G       13.5T   2% /lustre[OST:0]
        lustre-OST0001_UUID        14.5T      310.0G       13.5T   2% /lustre[OST:1]
        lustre-OST0002_UUID        14.5T      324.4G       13.5T   2% /lustre[OST:2]
        lustre-OST0003_UUID        14.5T      278.8G       13.5T   2% /lustre[OST:3]
        lustre-OST0004_UUID        14.5T      253.1G       13.6T   2% /lustre[OST:4]
        lustre-OST0005_UUID        14.5T      238.5G       13.6T   2% /lustre[OST:5]
        lustre-OST0006_UUID        14.5T      281.1G       13.5T   2% /lustre[OST:6]
        lustre-OST0007_UUID        14.5T      293.7G       13.5T   2% /lustre[OST:7]
        lustre-OST0008_UUID        14.5T      261.1G       13.6T   2% /lustre[OST:8]
        lustre-OST0009_UUID        14.5T      330.0G       13.5T   2% /lustre[OST:9]
        lustre-OST000a_UUID        14.5T      287.7G       13.5T   2% /lustre[OST:10]
        (the actual output is longer).

Optimising I/O
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The best way to get good performance to any filesystem (not just Lustre) is to do reads and writes in large chunks and to use large files rather than many small files.

What is “large” in each context? For Lustre, reads and writes of >1MB are ideal. 10 MB and above is best. Small (eg. < 100 kB) reads and writes and especially 4k random writes are a horrible thing to do to any file system and should be avoided if at all possible. They cause a lot of seeking and obtain low i/o performance from the underlying disks. Small sequential reads are often optimised by read-ahead in block devices or Lustre or on the OSTs so may perform ok, but they’re still not a great idea.

Optimal file sizes are usually between 10 MB and 100 GB. Using anything smaller than 10 MB files risks having its i/o time dominated by open()/close() operations, of which there are a limited amount available to the entire file system. A pathologically bad file usage pattern would be a code that uses 100,000 files, each of <8k in size. This will perform extremely badly on anything except a local SSD in your laptop. It is not a suitable usage model for a large shared supercomputer file system. Similarly, writing a code that has open()/close() in a tight inner loop will be completely dominated by the metadata operations to the Lustre MDS, will perform terribly, and will also impact the use of the cluster for all users because the MDS is a shared resource and can only do a finite number of operations per second.

The best way to get high I/O performance when reading or writing lots of data (eg. a checkpoint) from a large parallel code is generally to write one large O(GB) file per process, or if the number of processes is very large, then one file per node. This will send I/O to all or many of the OSTs that make up the filesystem and so could run as fast as 30 GB/s.

File lock bouncing is also an issue that can affect POSIX parallel file systems. This typically occurs when multiple nodes are appending to the same shared “log” file. However by its very nature the contents of the file are undefined, so it is really a “junk” file. However Lustre will valiantly attempt to interlace I/O from each appending node at the exact moment it writes, leading to a vast amount of “write lock bouncing” between all the appending nodes. This kills the performance all the processes appending, from the nodes doing the appending, and (worst of all) increases the load on the MDS greatly. Do not append to any shared files from multiple nodes. Do not write to any shared files from multiple nodes unless you are going via a library like MPI IO.