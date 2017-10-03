.. This file has been modified from the CPython version.
   Modifications are extensive, and normally this would mean we should write a
   new file index_jy.rst, but the expectation is that index.rst is the starting
   point for the documentation.
   We therefore keep it as modified, although merging from the CPython devguide
   is more difficult this way.

========================
Jython Developer's Guide
========================

This guide is a comprehensive resource for :ref:`contributing <contributing>`
to Jython_ -- for both new and experienced contributors,
and assuming we use GitHub for the master repositories.
It has been adapted from the CPython Developer's Guide and the reader should
bear in mind that:

*  The process is based on GitHub that CPython uses
   but not yet Jython.
*  The adaptation is imperfect: parts of the guide will say CPython.
*  When the guide says Python, sometimes it just means CPython.

This guide is :ref:`maintained <helping-with-the-developers-guide>` by the same
community that maintains CPython and Jython.
We welcome your contributions to those implementations of Python!


Quick Reference
---------------

.. highlight:: bash

Here are the basic steps needed to get :ref:`set up <setup>` and contribute a
patch. This is meant as a checklist, once you know the basics. For complete
instructions please see the :ref:`setup guide <setup>`.

1. Install and set up :ref:`Git <vcsetup>` and other dependencies
   (see the :ref:`Get Setup <setup>` page for detailed information).

2. Fork `the Jython repository <https://github.com/jython/jython>`_
   to your GitHub account and :ref:`get the source code <checkout>` using::

      git clone https://github.com/<your_username>/jython

3. Build Jython using::

      ant

   issued in the base check-out directory.
   The built application will be in subdirectory ``dist``.
   See also :ref:`more detailed instructions <compiling>`,
   :ref:`how to build dependencies <build-dependencies>`, and the
   plaform-specific pages for :ref:`UNIX <unix-compiling>`,
   and :ref:`Windows <windows-compiling>`.

4. :doc:`Run the tests <runtests>`::

      dist/bin/jython -m test -e

   With Jython 2.7, replace ``test`` with ``test.regrtest``.

5. Create a new branch where your work for the issue will go, e.g.::

      git checkout -b fix-issue-12345 master

   If an issue does not already exist, please `create it
   <https://bugs.python.org/>`_.  Trivial issues (e.g. typo fixes) do not
   require any issue to be created.

6. Once you fixed the issue, run the tests, and if
   everything is ok, commit.

7. Push the branch on your fork on GitHub and :doc:`create a pull request
   <pullrequest>`.  Include the issue number using ``bpo-NNNN`` in the
   pull request description.  For example::

      bpo-12345: Fix some bug in spam module

.. note::

   bpo stands for bugs.python.org and these flags are used by CPython's GitHub
   tools. For Jython we need our own naming convention, and to re-use the
   CPython tools. It is also worth considering whether we move away from
   bugs.jython.org to GitHub issues, and how we do that.

.. note::

   First time contributors will need to sign the Contributor Licensing
   Agreement (CLA) as described in the :ref:`Licensing <cla>` section of
   this guide.

Quick Links
-----------

Here are some links that you probably will reference frequently while
contributing to Jython:

* `Jython issue tracker`_
* `Buildbot status`_
* :doc:`help`
* PEPs_ (Python Enhancement Proposals)
* :doc:`gitbootcamp`

.. _branchstatus:

Status of Jython branches
-------------------------

.. note:: Maybe how it should look in a process based on GitHub. Not how it is.

+--------+----------+-------------+---------------+-------------+-----------------------------------------------------------------------------------------------------------------+
| Branch | Schedule | Status      | First release | End-of-life | Comment                                                                                                         |
+========+==========+=============+===============+=============+=================================================================================================================+
| master |          | features    |               |             | The default branch is currently the future Jython 3.5.                                                          |
+--------+----------+-------------+---------------+-------------+-----------------------------------------------------------------------------------------------------------------+
| 2.7    |          | bugfix      |               |             | The support has been extended to 2020 (1).                                                                      |
|        |          |             |               |             | Most recent binary release: `Jython 2.7.1                                                                       |
|        |          |             |               |             | <http://search.maven.org/remotecontent?filepath=org/python/jython-installer/2.7.1/jython-installer-2.7.1.jar>`_ |
+--------+----------+-------------+---------------+-------------+-----------------------------------------------------------------------------------------------------------------+
| 2.5    |          | end-of-life |               |             | Final release: `Jython 2.5.3                                                                                    |
|        |          |             |               |             | <https://repo1.maven.org/maven2/org/python/jython/2.5.3/>`_                                                     |
+--------+----------+-------------+---------------+-------------+-----------------------------------------------------------------------------------------------------------------+

(1) The exact date of Python 2.7 end-of-life has not been decided yet. It will
be decided by Python 2.7 release manager, Benjamin Peterson, who will update
the :pep:`373`. Read also the `[Python-Dev] Exact date of Python 2 EOL?
<https://mail.python.org/pipermail/python-dev/2017-March/147655.html>`_ thread
on python-dev (March 2017).

Status:

:features: new features are only added to the default branch, this branch
    accepts any kind of change.
:bugfix: bugfixes and security fixes are accepted, new binaries are still
    released.
:security: only security fixes are accepted and no more binaries are released,
    but new source-only versions can be released
:end-of-life: branch no longer maintained; no more changes can be pushed to
    this branch.

Dates in *italic* are scheduled and can be adjusted.

By default, the end-of-life is scheduled 5 years after the first release.  It
can be adjusted by the release manager of each branch. Versions older than 2.7
have reached end-of-life.
The Jython project follows the Python Software Foundation in the naming of
versions of the language (e.g. Jython 2.7 implements Python 2.7), and whether an
implementation of that version has reached reached end-of-life.

See also :ref:`Security branches <secbranch>`.


.. _contributing:

Contributing
------------

We encourage everyone to contribute to Python and that's why we have put up this
developer's guide.  If you still have questions after reviewing the material in
this guide, then the `Python Mentors`_ group is available to help guide new
contributors through the process.

A number of individuals from the Python community have contributed to a series
of excellent guides at `Open Source Guides <https://opensource.guide/>`_.

Core developers and contributors alike will find the following guides useful:

* `How to Contribute to Open Source <https://opensource.guide/how-to-contribute/>`_
* `Building Welcoming Communities <https://opensource.guide/building-community/>`_

Guide for contributing to Python:

* :doc:`setup`
* :doc:`help`
* :doc:`pullrequest`
* :doc:`runtests`
* Beginner tasks to become familiar with the development process
    * :doc:`docquality`
    * :doc:`coverage`
* Advanced tasks for once you are comfortable
    * :doc:`silencewarnings`
    * Fixing issues found by the :doc:`buildbots <buildbots>`
    * :doc:`fixingissues`
* :ref:`tracker` and :ref:`helptriage`
    * :doc:`triaging`
    * :doc:`experts`
* :doc:`communication`
* :doc:`coredev`
    * :doc:`committing`
    * :doc:`devcycle`
    * :doc:`buildbots`
    * :doc:`coverity`
* :doc:`gitbootcamp`

It is **recommended** that the above documents be read in the order listed.  You
can stop where you feel comfortable and begin contributing immediately without
reading and understanding these documents all at once.  If you do choose to skip
around within the documentation, be aware that it is written assuming preceding
documentation has been read so you may find it necessary to backtrack to fill in
missing concepts and terminology.


Proposing changes to Python itself
----------------------------------

Improving Python's code, documentation and tests are ongoing tasks that are
never going to be "finished", as Python operates as part of an ever-evolving
system of technology.  An even more challenging ongoing task than these
necessary maintenance activities is finding ways to make Python, in the form of
the standard library and the language definition, an even better tool in a
developer's toolkit.

While these kinds of change are much rarer than those described above, they do
happen and that process is also described as part of this guide:

* :doc:`stdlibchanges`
* :doc:`langchanges`


Other Interpreter Implementations
---------------------------------

This guide is specifically for contributing to Python on the JVM,
also known as Jython. (While most of the standard library is written in Python,
the interpreter core is written in Java and integrates most easily with the Java
SE and EE ecosystems).

There are other Python implementations, each with a different focus.  Like
Jython, they always have more things they would like to do than they have
developers to work on them.  Some major example that may be of interest are:

* CPython_: The reference implementation of Python implemented in C,
  and the main focus of language developement.
* PyPy_: A Python interpreter focused on high speed (JIT-compiled) operation
  on major platforms
* IronPython_: A Python interpreter focused on good integration with the
  Common Language Runtime (CLR) provided by .NET and Mono
* Stackless_: A Python interpreter focused on providing lightweight
  microthreads while remaining largely compatible with CPython specific
  extension modules


Key Resources
-------------

* Coding style guides
    * Jython's `Java coding standard <https://wiki.python.org/jython/CodingStandards>`_
    * :PEP:`8` (Style Guide for Python Code)
* `Issue tracker`_
    * `Meta tracker <http://psf.upfronthosting.co.za/roundup/meta>`_ (issue
      tracker for the issue tracker)
    * :doc:`experts`
* Source code
    * `Browse online <https://github.com/jython/jython/>`_
* PEPs_ (Python Enhancement Proposals)
* :doc:`help`

.. _resources:

Additional Resources
--------------------

* Anyone can clone the sources for this guide.  See
  :ref:`helping-with-the-developers-guide`.
* Help with ...
    * :doc:`exploring_jy`
    * :doc:`grammar_jy`
    * :doc:`compiler_jy`
* Tool support
    * Various tools with configuration files as found in the `Misc directory`_
    * Information about editors and their configurations can be found in the
      `wiki <https://wiki.python.org/moin/PythonEditors>`_

* :ref:`Search this guide <search>`


Code of Conduct
---------------
Please note that all interactions on
`Python Software Foundation <https://www.python.org/psf-landing/>`__-supported infrastructure is
`covered <https://www.python.org/psf/records/board/minutes/2014-01-06/#management-of-the-psfs-web-properties>`__
by the `PSF Code of Conduct <https://www.python.org/psf/codeofconduct/>`__, which includes all
infrastructure used in the development of Python itself (e.g. mailing lists, issue trackers, GitHub, etc.).
In general this means everyone is expected to be open, considerate, and respectful of others no matter what
their position is within the project.

.. _contents:

Full Table of Contents
----------------------

.. toctree::
   :maxdepth: 1
   :numbered:

   setup_jy
   help
   pullrequest
   runtests
   docquality
   documenting
   silencewarnings
   fixingissues
   tracker
   triaging_jy
   communication
   coredev
   committing
   devcycle
   buildbots_jy
   stdlibchanges
   langchanges
   experts
   exploring_jy
   grammar_jy
   compiler_jy
   gitbootcamp

Specific to CPython_
--------------------

These are sections from the CPython guide, retained for reference.
A comparison with the CPython implementation of a feature can help you
understand the Jython one.

.. We're also keeping these files for the technical reason that changes from
   CPython upstream can only be merged if these CPython files continue to exist.

.. toctree::
   :maxdepth: 1

   setup
   coverage
   triaging
   porting
   developers
   buildbots
   gdb
   exploring
   grammar
   compiler
   coverity
   clang
   buildslave
   motivations


.. _Buildbot status: https://www.python.org/dev/buildbot/
.. _Firefox search engine plug-in: https://www.python.org/dev/searchplugin/
.. _Misc directory: https://github.com/python/cpython/tree/master/Misc
.. _PEPs: https://www.python.org/dev/peps/
.. _python.org maintenance: https://pythondotorg.readthedocs.io/
.. _Python: https://www.python.org/
.. _CPython: https://www.python.org/
.. _Python Mentors: https://www.python.org/dev/core-mentorship/
.. _PyPy: http://www.pypy.org/
.. _Jython: http://www.jython.org/
.. _IronPython: http://ironpython.net/
.. _Stackless: http://www.stackless.com/
.. _Issue tracker: https://bugs.python.org/
.. _Jython issue tracker: https://bugs.jython.org/
