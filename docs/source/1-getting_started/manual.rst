.. highlight:: rst

How to use man pages
============================

In a linux environment, it is possible to learn more about a command like ``ls`` or ``cd`` (e.g. how to use it, what options or flags are available, etc.) by reading it's documentation manual. This is done via the ``man`` command, which formats and displays this documentation.

Simply enough, you can simply type ``man <command>`` to learn more about a linux command.

All man pages have a common format, which begins with the name of the command, a brief description of what it does, and further description of options/flags (e.g. ``-L``) and usage example, if applicable.

Here is an example of output for ``man ls``:

::

    LS(1)                                         User Commands                                        LS(1)

    NAME
           ls - list directory contents

    SYNOPSIS
           ls [OPTION]... [FILE]...

    DESCRIPTION
           List information about the FILEs (the current directory by default).  Sort entries alphabetically
           if none of -cftuvSUX nor --sort is specified.

           Mandatory arguments to long options are mandatory for short options too.

           -a, --all
                  do not ignore entries starting with .

           -A, --almost-all
                  do not list implied . and ..

           --author
                  with -l, print the author of each file

    ...
