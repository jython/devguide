.. Jython companion to setup.rst

===============
Getting Started
===============

These instructions cover how to get a working copy of the source code and a
compiled version of the Jython interpreter (Jython is the version of Python
available from http://www.jython.org/). It also gives an overview of the
directory structure of the Jython source code.

OpenHatch has a partly-relevant `setup guide`_ for CPython for people who are
completely new to contributing to open source.

.. _setup guide: http://wiki.openhatch.org/Contributing_to_Python


.. _setup-jy:

Getting Set Up
==============


.. _vcsetup-jy:

Version Control Setup (Mercurial)
---------------------------------

Jython is developed using `Mercurial <http://hg-scm.org/>`_. The Mercurial
command line program is named ``hg``; this is also used to refer to Mercurial
itself. Mercurial is easily available for common Unix systems by way of the
standard package manager; under Windows, you might want to use the
`TortoiseHg <http://tortoisehg.org/>`_ graphical client.

Version Control Setup (GitHub)
------------------------------

Jython will be developed using `git <https://git-scm.com>`_. The git
command line program is named ``git``.
git is easily available for all common operating systems. As the
Jython repo will be hosted on GitHub, please refer to either the
`GitHub setup instructions <https://help.github.com/articles/set-up-git/>`_
or the `git project instructions <https://git-scm.com>`_ for step-by-step
installation directions. You may also want to consider a graphical client
such as `TortoiseGit <https://tortoisegit.org/>`_ or
`GitHub Desktop <https://desktop.github.com/>`_.

You may also wish to set up :ref:`your name and email <set-up-name-email>`
and `an SSH key
<https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/>`_
that will allow you to interact with GitHub without typing a username
and password each time you execute a command, such as ``git pull``,
``git push``, or ``git fetch``.  On Windows, you should also
:ref:`enable autocrlf <autocrlf>`.

.. _checkout-jy:

Getting the Source Code
-----------------------

Mercurial
^^^^^^^^^
One should always work from a working copy of the Jython source code.
While it may
be tempting to work from the copy of Jython you already have installed on your
machine, it is very likely that you will be working from out-of-date code as
the Jython core developers are constantly updating and fixing things in their
:abbr:`VCS (version control system)`. It also means you will have better tool
support through the VCS as it will provide a diff tool, etc.

To get a working copy of the :ref:`in-development <indevbranch>` branch of
Jython (core developers use a different URL as outlined in :ref:`coredev`),
run::

    hg clone http://hg.python.org/jython

If you want a working copy of an already-released version of Jython,
i.e., a version in :ref:`maintenance mode <maintbranch>`, you can update your
working copy. For instance, to update your working copy to Jython 2.5, do::

   hg update 2.5

GitHub
^^^^^^
In order to get a copy of the source code you should first :ref:`fork the
Jython repository on GitHub <fork-jython>` and then :ref:`create a local
clone of your private fork and configure the remotes <clone-your-fork>`.

If you want a working copy of an already-released version of Python,
i.e., a version in :ref:`maintenance mode <maintbranch>`, you can checkout
a release branch. For instance, to checkout a working copy of Jython 2.5,
do ``git checkout 2.5``.

You will need to re-compile Jython when you do such an update.

.. _compiling-jy:

Compiling
---------

Compiling Jython is fairly simple, from the top level of a source checkout do::

    ant

.. _build-dependencies-jy:

Build dependencies
^^^^^^^^^^^^^^^^^^

The core Jython interpreter depends on a number of jars that are found in
directory ``extlibs``, and enumerated in ``build.xml``.

Running
^^^^^^^

Once Jython is done building you will then have a working build
that can be run in-place from ``./dist/bin/jython``.
There is normally no need to install your built copy of Jython.
The interpreter will adapt to the environment it is being run from
and use the files found in the working copy.

If you edit Jython's source code in your working copy,
changes to Python code will be picked up by the interpreter for immediate
use and testing.  (If you change Java code, you will need to recompile the
affected files as described above.)

.. _issue tracker: http://bugs.jython.org

Editors and Tools
=================

Python is used widely enough that practically all code editors have some form
of support for writing Python code. Various coding tools also include Python
support.

Jython specific support is less common but supported in several IDEs. Many of
the core developers do pretty well with Emacs or Vim :)

TODO: Eclipse

TODO: Netbeans

For editors and tools which the core developers have felt some special comment
is needed for coding *in* Python, see :ref:`resources`.


Directory Structure
===================

There are several top-level directories in the Jython source tree. Knowing what
each one is meant to hold will help you find where a certain piece of
functionality is implemented. Do realize, though, there are always exceptions to
every rule.

``Demo``
   Outdated Jython demo code.

   TODO: fix this to make it current.

``Doc``
   Outdated Jython website docs.

   TODO: Find an approach to the documentation given that most of it is
   exactly as produced by CPython and some of it must be totally different.

``lib-python``
   A recent copy of the Python standard library for the target version of
   Python, taken from the reference implementation CPython.

``Lib``
   The parts of the standard library we cannot simply adopt from CPython.
   These supersede or add to the modules in ``lib-python``.

``Misc``
   Things that do not belong elsewhere. Typically this is varying kinds of
   developer-specific tools and documentation.

``ast``
   Code related to the parser and the definition of the AST nodes.

   TODO: It may be a good idea to rename this directory "Parser" to match
   CPython.

``bugtests``
   Outdated framework for testing Jython.

   TODO: tests that remain here should be ported to Lib/tests/

``extlibs``
   External Java dependencies for Jython.

``grammar``
   ANTLR source for building Jython's grammar.

``maven``
   Source for including Jython in Maven.

``src``
   The part of the Jython interpreter that is implemented in Java.

``tests``
   Tests for Jython implemented in Java.

``Tools``
   Various tools that are (or have been) used to maintain Jython.

