.. highlight:: rst

File systems
=============

Directories
-------------

**OzSTAR**

On OzSTAR, The two main directories are: ::

    /home

    /fred/projects

noting that ``/projects`` is soft-linked to ``/fred/projects``.

As opposed to Green II, both ``/home`` and ``/projects`` directores are using the Lustre filesystem. The ``/home`` directory is backed up. It has a 10GB quota, and 100,000 files. The ``/projects`` directories are on a lustre filesystem. This is where all of your project data should be stored. There is **NO backup for** ``/projects`` **and the user must take responsibility for backing up any data that is important.**

----------

**Green II**

On Green II, the two main directories are: ::

    /home

    /lustre/projects

noting that ``/projects`` is soft-linked to ``/lustre/projects``.

The ``/home`` directory is on NFS and is backed up. It has a 10GB quota. The ``/projects`` directories are on a lustre filesystem. This is where all of your project data should be stored. There is **NO backup for** ``/projects`` **and the user must take responsibility for backing up any data that is important.**


Lustre
------

**OzSTAR**

On OzSTAR, we have ~ 5 Petabytes of Lustre-zfs Filesystem for the OzSTAR nodes. The achievable bandwidth into each OSS (Object Storage Server) is approximately 100 Gb/s. *More information to come*.

----------

**Green II**

On Green II, we have ~ 3 Petabytes of Lustre Filesystem for the gstar and sstar nodes. At the moment, we have 1 MDS (MetaData Server) to record the metadata of your files and 12 OSSs that serve out data. Each OSS has 8 to 11 OSTs (Object Storage Target). Each OST is a RAID6 or DDP-11 or DDP-20 array of about 14 to 28 TB in capacity.

The achievable bandwidth into each OSS is approximately 1.1 GB/s. The performance of each OST is approximately 350 to 500 MB/s for a single stream of i/o.

In total we have 1180 hard drives and together they are configured as RAID 6 on each of the OSTs. This drive count will increase as part of the upgrade.

Lustre manuals are at `Intel HPDD Wiki <https://wiki.hpdd.intel.com/display/PUB/Documentation>`_

Dealing with Lustre
-------------------

1. Striping
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
``lfs getstripe/lfs setstripe``

The use of Lustre striping is **highly discouraged**. Improvements due to striping are limited to a few special and relatively unusual cases in a busy HPC environment, and is only ever acceptable for very large files. The extensive use of wide-striping is highly detrimental to the performance and usability of the Lustre file system as a whole. Do not use striping unless you are sure that it is the correct thing to do.

For more details, please refer to the `NERSC page on striping <http://www.nersc.gov/users/data-and-file-systems/optimizing-io-performance-for-lustre/>`_. Below are listed the criteria for which the use of striping is recommended on g2.

**Use cases for which the use of Lustre striping is acceptable:**

- Huge files that are >few TB in size should be striped so that they do not unduly fill up single OSTs. Lustre file systems are made up of many separate reliable OSTs. Our OSTs are each ~28TB in size. Choose striping so that there is < say 500 GB per OST. So if it was a 5 TB file, then set a striping of 10. Huge files are unwieldy and not recommended for any file system.
- Large (>10 GB) single files that are read and written using the MPI IO library from one large parallel job. The MPI IO library allows multiple readers and writers to access and modify a single shared file at the same time. Striping the file such that the number of stripes are << the number of nodes used by the parallel job should result in some speedup in i/o accesses. Suggested striping might be 4 for a 10GB file.

Striping files that are < few GB in size is strongly discouraged. A single process can easily read and write at 300 MB/s to a single un-striped file on Lustre. So a 1GB un-striped file can be read in approximately 3 seconds. There will be much collateral damage to the usability of the whole file system if small 1 GB files are wide-striped. This includes aggregate performance loss due to seek amplification on OSTs and vast interactive performance loss (ls -l could take days) due to not being able to cache OST inodes on the file system servers.

If your files meet the above criteria, or you have previously cleared your use case for striping with the SUT HPC team, then instructions for setting striping on files or directories are below.

Lustre file systems have the ability to stripe data over multiple OSTs. The stripe count can be set on a file system, directory, or file level. Although individual tests may show that striping of >1 improves the read and write performance of > 1GB files on Lustre, overall the use of many striped files will reduce individual and aggregate performance.

To see the current stripe size:
::

    [username@g2 ~]$ lfs getstripe /home/username
    /home/username
    stripe_count:   1 stripe_size:    1048576 stripe_offset:  -1
    /home/username/.bash_profile
    lmm_stripe_count:   1
    lmm_stripe_size:    1048576
    lmm_stripe_offset:  72
        obdidx   objid    objid   group
             7  267113  0x41369       0

The file ``/home/username/.bash_profile`` is set to stripe in 1 OST and all the writes can start from any OST (stripe offset= -1). The stripe size is set to 1M.

For the command to set stripe, best practise is always leave the stripe offset (-i) as default which is -1. This allows the MDS to choose the starting index. This setting is strongly recommended, as it allows space and load balancing to be done by the MDS as needed. Otherwise, the file starts on the specified OST index.

If you pass the index to 0 and stripe count value of 1, all the files will be written to OST0 until the space is exhausted. This is probably not what you want to do.

If you only want to adjust the stripe count (how many nodes the files write to) and keep the other parameter at their default settings, do not specify any of the other parameters.

To set the stripe count:
::

    [username@g2 ~]$ lfs setstripe -c 3 cuda

Directory cuda has been set to stripe across 3 OSTs, you can see the output by using the “lfs getstripe” command.
::

    [username@g2 ~]$ lfs getstripe cuda
    cuda
    stripe_count: 3 stripe_size: 0 stripe_offset: -1


So when I create a new file inside the cuda directory, it will stripe to 3 OSTs,
::

    [username@g2 cuda]$ lfs getstripe test_of_3_osts
    test_of_3_osts
    lmm_stripe_count:   3
    lmm_stripe_size:    1048576
    lmm_stripe_offset:  107
        obdidx		 objid		objid		 group
           107	        626536	      0x98f68	             0
             4	        644037	      0x9d3c5	             0
            14	        644985	      0x9d779	             0

The command to set the start index and stripe size:
::

    [username@g2 ~]$ lfs setstripe -s 4M -i 1 -c 2 cuda
    [username@g2 ~]$ lfs getstripe cuda
    cuda
    stripe_count:   2 stripe_size:    4194304 stripe_offset:  1

The first command shows that the cuda directory has been set to stripe to 2 OSTs, starts from the OST01 and the stripe size is 4M.

Thus, when I create a file in the cuda directory, the new file gets the folder’s stripe size.
::

    [username@g2 cuda]$ touch test
    [username@g2 cuda]$ lfs getstripe test
    test
    lmm_stripe_count:   2
    lmm_stripe_size:    4194304
    lmm_stripe_offset:  1
        obdidx		 objid		objid		 group
             1	        644587	      0x9d5eb	             0
             2	        644065	      0x9d3e1	             0

Further command explanation:

``lfs setstripe``

-s            Stripe size (k,m,g). Good stripe is between 1 MB and 4 MB

-i            OST index of first stripe (default value is -1) Default is recommended.

-c            Stripe count, number of OST(s) to stripe over (0 default, -1 all)

2. Quota
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Quotas have been enabled on both OzSTAR and Green II.

On **OzSTAR**, the default quota is 10Tb for projects, and 100,000*10 files for projects. If you need extra storage, please contact hpc-support@swin.edu.au

On **Green II**, the default quota is 500GB for projects. If you need extra storage, please contact hpc-support@swin.edu.au

To check your quota:

``lfs quota /lustre``

Output:
::

    [username@pbs ~]$ lfs quota  /lustre
    Disk quotas for user username (uid 1000):
         Filesystem  kbytes   quota   limit   grace   files   quota   limit   grace
            /lustre [1041408]  5242880 6291456       -     [0]       0       0       -

There is also a helpful command for checking the usage of all projects you are associated with. Just type

user-quota
(this needs to be done on the head node g2).

3. Free space
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

4. Optimising I/O
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The best way to get good performance to any filesystem (not just Lustre) is to do reads and writes in large chunks and to use large files rather than many small files.

What is “large” in each context? For Lustre, reads and writes of >1MB are ideal. 10 MB and above is best. Small (eg. < 100 kB) reads and writes and especially 4k random writes are a horrible thing to do to any file system and should be avoided if at all possible. They cause a lot of seeking and obtain low i/o performance from the underlying disks. Small sequential reads are often optimised by read-ahead in block devices or Lustre or on the OSTs so may perform ok, but they’re still not a great idea.

Optimal file sizes are usually between 10 MB and 100 GB. Using anything smaller than 10 MB files risks having its i/o time dominated by open()/close() operations, of which there are a limited amount available to the entire file system. A pathologically bad file usage pattern would be a code that uses 100,000 files, each of <8k in size. This will perform extremely badly on anything except a local SSD in your laptop. It is not a suitable usage model for a large shared supercomputer file system. Similarly, writing a code that has open()/close() in a tight inner loop will be completely dominated by the metadata operations to the Lustre MDS, will perform terribly, and will also impact the use of the cluster for all users because the MDS is a shared resource and can only do a finite number of operations per second.

The best way to get high I/O performance when reading or writing lots of data (eg. a checkpoint) from a large parallel code is generally to write one large O(GB) file per process, or if the number of processes is very large, then one file per node. This will send I/O to all or many of the OSTs that make up the filesystem and so could run as fast as 12 GB/s.

File lock bouncing is also an issue that can affect POSIX parallel file systems. This typically occurs when multiple nodes are appending to the same shared “log” file. However by its very nature the contents of the file are undefined, so it is really a “junk” file. However Lustre will valiantly attempt to interlace I/O from each appending node at the exact moment it writes, leading to a vast amount of “write lock bouncing” between all the appending nodes. This kills the performance all the processes appending, from the nodes doing the appending, and (worst of all) increases the load on the MDS greatly. Do not append to any shared files from multiple nodes. Do not write to any shared files from multiple nodes unless you are going via a library like MPI IO.