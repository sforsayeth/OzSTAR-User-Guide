.. highlight:: rst

File Transfer
======================================================

In order to transfer files to and from the supercomputer (OzSTAR), a ssh-based file transfer utility is required. There are a variety of Options that you can use.

+------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Operating System | Terminal                                                                                                                                                                  |
+------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Mac              | `“scp” (secure copy) command <http://www.computerhope.com/unix/scp.htm>`_ can be used via the terminal or use `Cyberduck <https://cyberduck.io/>`_ as a GUI alternative   |
+------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Linux            | `“scp” (secure copy) command _` can be used via the terminal                                                                                                              |
+------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Windows          | The recommended software is `WinSCP <http://winscp.net/eng/index.php>`_                                                                                                   |
+------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

As an alternative option, you can use a SFTP Client (e.g `FileZilla <https://filezilla-project.org/>`_ Client – more details about using the SFTP mode in FileZilla can be found on https://it.unh.edu/sftp/filezilla.html)


Copying a file or directory via SSH
---------------------------------------

The simplest way to copy a file to or from the supercomputer is to use the ``scp`` command.

.. note::

    In the following examples, we assume an ``ozstar`` alias set to ``[your-username]@ozstar.swin.edu.au``.

**Copying a local file to the OzSTAR supercomputer**::

    scp ./file.txt ozstar:destination/path/

You can also copy a file from the supercomputer to your local machine (e.g. **download**) as follows::

    scp ozstar:path/to/file.txt .

**Copy a directory and its content** is done by using the ``-r`` option::

    scp -r path/to/copy/ ozstar:destination/path/

The above command will initiate a connection from your local environment to OzSTAR directly.

Transferring a large number of small files
--------------------------------------------

Transferring lots of small files can take a long time with ``scp`` due to the overhead of copying each file individually. Instead, it is advised to package your files into a single ``tar`` file using the `tar <https://www.gnu.org/software/tar/manual/html_section/tar_22.html>`__ command to significantly reduce the transfer time. This is done by create a tar archive, copying it using ``scp``, and finally ``untar`` it to retrieve your individual files.

.. Transferring large files
    ----------------------------

    When transferring large files, it is often interesting to use the ``-C`` option of ``scp`` to first compress the file, send it, and then decompress it. Using it simply with

    ::

        scp -C ./large_file.txt ozstar:destination/path/

Transferring code
----------------------
The best way to transfer code from one computer to another is to host the code in a *source code repository* using a *versioning system* such as `git <https://www.git-scm.com>`__ or `mercurial <https://www.mercurial-scm.org>`__ and clone the repository from your local computer, or a cloud service (e.g. bitbucket, github, ...), to the supercomputer.

Resuming interrupted transfers
--------------------------------

If a transfer is interrupted, you might end up with part of the files being transferred. Rather than restarting the transfer from scratch, you should then use the rsync command. The `rsync <https://linux.die.net/man/1/rsync>`__ command will compare the **source** and **destination** directories and only transfer what needs to be transferred (missing files, modified files, etc.)

::

    rsync -va ./source_dir ozstar:destination/path

.. warning::

    Make sure not to leave trailing slashes in your path names (e.g. ``destination/path`` rather than ``destination/path/``); else you will have a full copy of the directory inside the existing, partial, one. Use the ``-n`` (dry-run) option of ``rsync`` to check what will happen before you run the actual command.

.. tip::

    If one large file is left partially transferred, you can resume it using the ``--partial``.


Synchronising with a local directory
--------------------------------------------
If you want to keep two directories (one on your local computer, and one on the supercomputer) in sync, you can do that with rsync using its ``--delete`` option. But that is only one-way so you need to really think in what direction you do it, and it does not scale beyond two synchronized directories.

A real option is to use Unison, a piece of software that can detect and handle conflicts (incompatible changes made to the same file in the two directories that must be kept in sync.)