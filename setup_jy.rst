===============
Getting Started
===============

.. warning:: At present, this is not much more than a copy of the CPython original
   with the obviously inapplicable crudely hacked out.

These instructions cover how to get a working copy of the source code and a
compiled version of the Jython interpreter (Jython is the version of Python
available from https://www.jython.org/). It also gives an overview of the
directory structure of the Jython source code.

.. warning:: They mean CPython

OpenHatch also has a great `setup guide`_ for Python for people who are
completely new to contributing to open source.

.. _setup guide: http://wiki.openhatch.org/Contributing_to_Python


.. _setup-jy:

Getting Set Up
==============


.. _vcsetup-jy:

Version Control Setup
---------------------

CPython is developed using `git <https://git-scm.com>`_. The git
command line program is named ``git``; this is also used to refer to git
itself. git is easily available for all common operating systems. As the
CPython repo is hosted on GitHub, please refer to either the
`GitHub setup instructions <https://help.github.com/articles/set-up-git/>`_
or the `git project instructions <https://git-scm.com>`_ for step-by-step
installation directions. You may also want to consider a graphical client
such as `TortoiseGit <https://tortoisegit.org/>`_ or
`GitHub Desktop <https://desktop.github.com/>`_.

You may also wish to set up :ref:`your name and email <set-up-name-email>`
and `an SSH key
<https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/>`_
as this will allow you to interact with GitHub without typing a username
and password each time you execute a command, such as ``git pull``,
``git push``, or ``git fetch``.  On Windows, you should also
:ref:`enable autocrlf <autocrlf>`.

.. _checkout-jy:

Getting the Source Code
-----------------------

In order to get a copy of the source code you should first :ref:`fork the
Python repository on GitHub <fork-cpython>` and then :ref:`create a local
clone of your private fork and configure the remotes <clone-your-fork>`.

If you want a working copy of an already-released version of Python,
i.e., a version in :ref:`maintenance mode <maintbranch>`, you can checkout
a release branch. For instance, to checkout a working copy of Python 3.5,
do ``git checkout 3.5``.

You will need to re-compile CPython when you do such an update.

Do note that CPython will notice that it is being run from a working copy.
This means that if you edit CPython's source code in your working copy,
changes to Python code will be picked up by the interpreter for immediate
use and testing.  (If you change C code, you will need to recompile the
affected files as described below.)

Patches for the documentation can be made from the same repository; see
:ref:`documenting`.

.. _compiling-jy:

Compiling (for debugging)
-------------------------

CPython provides several compilation flags which help with debugging various
things. While all of the known flags can be found in the
``Misc/SpecialBuilds.txt`` file, the most critical one is the ``Py_DEBUG`` flag
which creates what is known as a "pydebug" build. This flag turns on various
extra sanity checks which help catch common issues. The use of the flag is so
common that turning on the flag is a basic compile option.

You should always develop under a pydebug build of CPython (the only instance of
when you shouldn't is if you are taking performance measurements). Even when
working only on pure Python code the pydebug build provides several useful
checks that one should not skip.


.. _build-dependencies-jy:

Build dependencies
''''''''''''''''''

.. note::

   Must talk here about git, ant ... what else? IDEs maybe?
   And must we explain how to install them?


.. _unix-compiling-jy:


UNIX
''''

The basic steps for building Jython for development is to configure it and
then compile it.


.. _issue tracker: https://bugs.jython.org




.. _windows-compiling-jy:

Windows
'''''''



.. _build-troubleshooting-jy:

Troubleshooting the build
-------------------------

This section lists some of the common problems that may arise during the
compilation of Python, with proposed solutions.


Editors and Tools
=================

Python is used widely enough that practically all code editors have some form
of support for writing Python code. Various coding tools also include Python
support.

For editors and tools which the core developers have felt some special comment
is needed for coding *in* Python, see :ref:`resources`.


Directory Structure
===================

There are several top-level directories in the CPython source tree. Knowing what
each one is meant to hold will help you find where a certain piece of
functionality is implemented. Do realize, though, there are always exceptions to
every rule.


``Lib``
     The part of the standard library implemented in pure Python.

``Misc``
     Things that do not belong elsewhere. Typically this is varying kinds of
     developer-specific documentation.

``src/org/python/core``
     Code for all built-in types.

``Tools``
     Various tools that are (or have been) used to maintain Python.

