.. highlight:: rst

FAQ
============================

Who is eligible for an account on the facility?
--------------------------------------------------------

All Swinburne staff and students are eligible for accounts as are researchers in the field of astronomy and space sciences based at publicly funded institutions within Australia. International collaborators may also apply for accounts but cannot be the lead on a research project.

How do I access the facility?
------------------------------------------

To access OzSTAR, all access is down through the head node ``ozstar.hpc.swin.edu.au``. Upon login, you will be assigned to one of two nodes, namely ``farnarkle1`` and ``farnarkle2``, in a round robin fashion. From there you can submit jobs to the queue.
These nodes can be used to run small tasks interactively. However, for larger jobs that require interaction, you should request an interactive node via Slurm (for more information, see :ref:`interactive_jobs`).

For more details, see the page :doc:`../1-getting_started/Access`.

What operating system is used?
------------------------------------------

All nodes of OzSTAR are running `CentOS 7 <http://wiki.centos.org/Manuals/ReleaseNotes/>`_ as the main operating system. As such, we advise that you get yourself familiar with the Linux operating system and the use of command line before using the supercomputer.


How do I change my login password?
------------------------------------------

- You can change your password when you are logged on, just type ``passwd`` in your terminal. You can find more details on `passwd manual page <http://man7.org/linux/man-pages/man1/passwd.1.html>`_.

- You can reset your password from `supercomputing.swin.edu.au/account-management/new_account_request <https://supercomputing.swin.edu.au/account-management/new_account_request>`__. There is a Login/Reset Password link on the left hand pane.

How can I change my login shell?
------------------------------------------

You can change your login shell, just type ``changeShell`` in your terminal. This information will take a few minutes to propagate, and you will have to login again for it to take effect.

How can I avoid session timeouts?
------------------------------------------

There are a few ways to do this but a simple method is to go to the .ssh directory on your host machine and create a file called “config”. In that file place the line
::

    ServerAliveInterval 120

and that should do it, although on some machines you may need to also run the command
::

    chmod 600 ~/.ssh/*

to ensure the file has the correct permissions.

How can I find out more about using GPUs?
---------------------------------------------

If you would like to learn more about using graphics programming units (GPUs) in your research a good starting point is the `NVIDIA Developer Zone <https://developer.nvidia.com/category/zone/cuda-zone>`_ or our `HPC/GPU webinar series <https://supercomputing.swin.edu.au/hpcgpu-webinars/>`_.

You can also look through slides on `Getting Started <http://astronomy.swin.edu.au/supercomputing/Swin_Getting_Started_with_CUDA_static.pdf>`_ with the CUDA programming language for GPUs, an introduction to the `OpenACC <http://astronomy.swin.edu.au/supercomputing/Swin_Intro_to_OpenACC_static.pdf>`_ directives for accelerating code with GPUs and the `Thrust <http://astronomy.swin.edu.au/supercomputing/thrust.pdf>`_ acceleration library.

These were all presented at a recent CUDA Easy workshop at Swinburne (thanks to Michael Wang, Paul Mignone, Amr Hassan and Luke Hodkinson).

Another great starting point is to go to the `GPU Technology Conference <GPU Technology Conference>`_ website and search through past presentations using their On-Demand tool. You can search by field and/or topic, for example, and most likely someone in your field has already tackled what you are hoping to do.

Why am I getting “Disk quota exceeded” message?
---------------------------------------------------

As an OzSTAR or gSTAR/SwinSTAR user, you may be a member of more than one group. In order to list these groups, you can use the command

::

    groups

Each group has its own storage quota, which is shared between its members. If you want to get the quota information of a specific group on lustre, you can use

::

    lfs quota -g  /lustre

The problem for some users recently has been that the files or directories created under a project directory (e.g ``/lustre/projects/``) have not been created with the correct group ownership because of either they inherit wrong group ownership, or they have incorrect group association. The following commands might help you to fix this issue:

- To find the files and the folders owned by a specific group, you can use

::

    find  ­group

- In order to map the contents of a certain folder to a specific group you can use

::

    chgrp -R  /path/to/folder

This will change the group ownership of all files and folders (including sub-folder) within a particular directory.

In order to ensure that new files and folders are created with your project group ownership, you can use
::

    find . /path/to/folder -type d -exec chmod g+s ‘{}’ \;

This will set a special permission so that new files are created with the assigned group owner of that folder. The path used for ‘path/to/folder’ would typically be your working directory.

You will probably face the disk quota exceeded issue because one or more of your directories are associated to the general group “cas”. In order to find all the files and the directories associated to “cas” in a specific path and change them to your group you can use
::

    find /path/to/folder -group cas -exec chgrp  ‘{}’ \;

Also, Users should be aware that just because a folder is owned by the correct group does not mean that files created under that folder will be. An example of this would be:
::

    drwxrwxr-x 1 joe p001_swin 4096 Jul 10 12:00 temp_folder

versus:
::

    drwxrwsr-x 1 joe p001_swin 4096 Jul 10 12:00 temp_folder

Only in the second example, new files and folders will be created under the ownership of the “p001_swin” group. The important permission here is the group stickybit. This is set easily with
::

    chmod g+s /path/to/folder

Any user with write access will be allowed to change this bit.

The other thing that you should be aware of is the use of ``rsync``. A very typical ``rsync`` command method is to simply use the ``-av`` options. This is a preset for archive, which includes a particular flag for permission preservation. What happens with this is that new writes or syncs of existing files will carry the permission bits of the source files and directories, which may overwrite the existing permission where we have already set the stickybit for inheritance on lustre or on nfs cluster. If you are writing data into some of these directories via rsync, you should ensure your source files also have at least the stickybit applied before transferring, or you could rsync without the ``-p`` option in your command to inherit the destination.