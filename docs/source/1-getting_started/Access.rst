.. highlight:: rst

Access the supercomputer
==========================

Access OzSTAR
-------------------

*More information will be provided soon.*



Access Green II
-------------------

Access to the supercomputer is available through Secure Shell (SSH) to g2.hpc.swin.edu.au for all users. i.e:
::

    ssh [your-username]@g2.hpc.swin.edu.au

.. important::

    The g2 head-node should only be used as an entry point to the supercomputer, for submitting jobs to the queue nodes, and transferring files (single process only). **Compute jobs found to be running directly on the head-node will be removed without notice.**

We have a set of interactive nodes (**gstar001**, **gstar002**, **sstar001**, **sstar002**, and **sstar003**) that can be used to run short jobs directly, compile code, or test your application. These nodes can be accessed from the g2 head node via ssh too. i.e:

::

    ssh gstar001

**Please, do not use these interactive nodes to run long jobs or jobs with big computational requirements.** Running such jobs in interactive mode can be done by requesting interactive nodes from the queue. Jobs running on interactive nodes can use up to 80% of the nodes total memory with a maximum of 4 GB of swap.

.. tip::

    If you want to avoid having to type the rather long ``[your-username]@g2.hpc.swin.edu.au`` every time you want to connect to the supercomputer, you can set an `alias <https://www.gnu.org/software/bash/manual/html_node/Aliases.html>`__.

    For example, if you are using the bash shell, you can set an alias in your bash configuration file (e.g. ``~/.bashrc`` or ``~/.bash_profile``) as follows:

    ::

        alias g2="[your-username]@g2.hpc.swin.edu.au"

    Once set, you can directly use this alias in your commands. The following commands will produce the same outcome:

    **Connect to supercomputer**::

        ssh [your-username]@g2.hpc.swin.edu.au

        ssh g2

    **Copy a local file to supercomputer**::

        scp myfile.txt [your-username]@g2.hpc.swin.edu.au:path/to/location/

        scp myfile.txt g2:path/to/location/


Client Requirements
--------------------

Terminal
^^^^^^^^

A terminal is required to access Green II via Secure Shell (SSH) and issue command line instructions.

+------------------+--------------------------------------------------------------+
| Operating System | Terminal                                                     |
+==================+==============================================================+
| Mac              | Available by default via Applications > Utilities > Terminal |
+------------------+--------------------------------------------------------------+
| Linux            | Available by default                                         |
+------------------+--------------------------------------------------------------+
| Windows          | The recommended software is Putty                            |
+------------------+--------------------------------------------------------------+

X11 Windows Forwarding
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you prefer to use the graphical screen of your application rather than a terminal, the ability to forward a graphical screen from the application back to your home computer is available via X11 Windows Forwarding.

With Linux and MacOS this can be done by login from the command line Secure Shell with the -X option (for X forwarding):

::

    ssh -X [username]@g2.hpc.swin.edu.au

For **MacOS** you will first need to install `XQuartz <https://www.xquartz.org/>`_.

For **Windows** you will need to install an X11 Server implementation. The following options can be used:

- `Cygwin/X <http://x.cygwin.com/>`_
- `Xming <http://sourceforge.net/projects/xming/files/Xming/>`_

You will need also enable X11 forward from Putty, before connecting, This can be done through **connection > SSH > X11** by selecting “**Enable X11 Forwarding**”.