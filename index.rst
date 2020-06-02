.. This file has been extensively modified from the CPython version.
   Normally this would mean a new file index_jy.rst, but index.rst is the
   starting point for the documentation.
   We therefore keep it as modified, although merging changes from the CPython
   devguide will be difficult for this file.

========================
Jython Developer's Guide
========================

This guide is a comprehensive resource for :ref:`contributing <contributing>`
to Jython_ -- for both new and experienced contributors.
It has been (somewhat incompletely)
adapted from the CPython Developer's Guide and the reader should
bear in mind that:

*  The Jython process, like CPython's, is based GitHub PRs,
   but Jython does not have the same set of tool integrations.
*  Jython migrated from Mercurial in 2020, much later than CPython,
   so Jython-specific parts of the guide may refer to the old process.
*  Statements about "Python" should apply to both CPython and Jython.
   The adaptation is imperfect: parts of the guide will say (or mean) CPython.

This guide is :ref:`maintained <helping-with-the-developers-guide>` by the same
community that maintains CPython and Jython.
We welcome your contributions to either implementation of Python!


Quick Reference
---------------

Mercurial
^^^^^^^^^

Mercurial is no longer used.
The previous official repository at ``https://hg.python.org/jython`` is not current.


GitHub
^^^^^^
.. highlight:: bash

Here are the basic steps needed to get :ref:`set up <setup-jy>` and contribute a
patch. This is meant as a checklist, once you know the basics. For complete
instructions please see the :ref:`setup guide <setup-jy>`.

1. Install and set up :ref:`Git <vcsetup-jy>` and other dependencies
   (see the :ref:`Get Setup <setup-jy>` page for detailed information).

2. Fork the `Jython GitHub repository`_
   to your GitHub account and :ref:`get the source code <checkout-jy>` using::

      git clone https://github.com/<your_username>/jython

3. Build Jython using::

      ant

   issued in the base check-out directory.
   The built application will be in subdirectory ``dist``.
   See also :ref:`more detailed instructions <compiling-jy>`,
   and :ref:`how to build dependencies <build-dependencies-jy>`.

4. :doc:`Run the tests <runtests_jy>`::

      dist/bin/jython -m test.regrtest -e

   This applies for Jython 2.7.
   To test Jython 3, when it exists, replace ``test.regrtest`` with ``test``.

5. Create a new branch where your work for the issue will go, e.g.::

      git checkout -b fix-issue-12345 master

   If an issue does not already exist, please create it on
   the `Jython GitHub repository`_.
   The legacy `bug tracker <https://bugs.jython.org/>`_ still operates
   (for Jython 2.7 only) but is not preferred.
   Trivial issues (e.g. typo fixes) do not require any issue to be created:
   create a PR directly.

6. Once you fixed the issue, run the tests, and if
   everything is ok, commit.

7. Push the branch to your fork on GitHub and :doc:`create a pull request
   <pullrequest>`.
   Include the issue number using GitHub conventions in the pull request
   description.
   (Use ``bjo-NNNN`` if it is from the legacy tracker.)

.. note::

   bpo-NNNN is used by CPython for for bugs.python.org
   and is picked up by CPython's GitHub tools.
   For Jython we need our own naming convention, but cannot yet re-use the
   CPython tools.
   We intend to move away from bugs.jython.org to GitHub issues more aggressively
   that CPython has made the switch.

.. note::

   First time contributors will need to sign the Contributor Licensing
   Agreement (CLA) as described in the :ref:`Licensing <cla>` section of
   this guide.
   This requires an account on the CPython's legacy bug tracker.


Quick Links
-----------

Here are some links that you probably will reference frequently while
contributing to Jython:

* `Jython GitHub repository`_ (or maybe the `Jython issue tracker`_)
* :doc:`help`
* PEPs_ (Python Enhancement Proposals)
* :doc:`gitbootcamp`

.. _branchstatus:

Status of Jython branches
-------------------------


+--------+----------+-------------+---------------+-------------+-----------------------------------------------------------------------------+
| Branch | Schedule | Status      | First release | End-of-life | Comment                                                                     |
+========+==========+=============+===============+=============+=============================================================================+
| master |          | bugfix      |               |             | PSF support for Python 2 ended in 2020 (see :pep:`373`) but                 |
|        |          |             |               |             | the most recent release of Jython is still `Jython 2.7.2                    |
|        |          |             |               |             | <https://search.maven.org/artifact/org.python/jython-installer/2.7.2/jar>`_ |
+--------+----------+-------------+---------------+-------------+-----------------------------------------------------------------------------+
| 3.8    |          | features    |               |             | This branch is the future of Jython                                         |
|        |          |             |               |             | (but just a gleam in the eye at present).                                   |
+--------+----------+-------------+---------------+-------------+-----------------------------------------------------------------------------+
| 2.5    |          | end-of-life |               |             | Final release: `Jython 2.5.3                                                |
|        |          |             |               |             | <https://repo1.maven.org/maven2/org/python/jython/2.5.3/jar>`_              |
+--------+----------+-------------+---------------+-------------+-----------------------------------------------------------------------------+


Status:

:features: new features are only added to the master branch, this branch
    accepts any kind of change.
:bugfix: bugfixes and security fixes are accepted, new binaries are still
    released.
:security: only security fixes are accepted and no more binaries are released,
    but new source-only versions can be released
:end-of-life: release cycle is frozen; no further changes can be pushed to it.

Dates in *italic* are scheduled and can be adjusted.

By default, the end-of-life is scheduled 5 years after the first release.  It
can be adjusted by the release manager of each branch. Versions older than 2.7
have reached end-of-life.
The Jython project follows the Python Software Foundation in the naming of
versions of the language (e.g. Jython 2.7 implements Python 2.7), and whether an
implementation of that version has reached reached end-of-life.
The third element (micro) has bears no relationship to CPython micro-versions.

See also :ref:`Security branches <secbranch>`.

Jython names its releases in the same scheme as CPython.
Each release of Jython is tagged in the source repo with a tag of the form
``vX.Y.ZTN``, where ``X`` is the major version, ``Y`` is the
minor version, ``Z`` is the micro version, ``T`` is the release level
(``a`` for alpha releases, ``b`` for beta, ``rc`` release candidate,
and *null* for final releases), and ``N`` is the release serial number.
Some examples of release tags: ``v3.7.0a1``, ``v3.6.3``, ``v2.7.14rc1``.


.. _contributing:

Contributing
------------

We encourage everyone to contribute to Python and that's why we have put up this
developer's guide.  If you still have questions after reviewing the material in
this guide, then the `Python Mentors`_ group is available to help guide new
contributors through the process.

.. FIXME: do Python Mentors help Jython contributors with process?

A number of individuals from the Python community have contributed to a series
of excellent guides at `Open Source Guides <https://opensource.guide/>`_.

Core developers and contributors alike will find the following guides useful:

* `How to Contribute to Open Source <https://opensource.guide/how-to-contribute/>`_
* `Building Welcoming Communities <https://opensource.guide/building-community/>`_

Guide for contributing to Jython:

* :doc:`setup_jy`
* :doc:`help`
* :doc:`patch_hg_jy` (existing process)
* :doc:`pullrequest` (partly usable future process)
* :doc:`runtests_jy`
* Beginner tasks to become familiar with the development process
    * :doc:`docquality`
    * :doc:`coverage`
* Advanced tasks for once you are comfortable
    * :doc:`silencewarnings`
    * Fixing issues found by the :doc:`buildbots <buildbots_jy>`
    * Helping out with reviewing `open pull requests`_.
      See :ref:`how to review a Pull Request <how-to-review-a-pull-request>`.
    * :doc:`fixingissues`
* :ref:`tracker` and :ref:`helptriage`
    * :doc:`triaging_jy`
    * :doc:`experts`
* :doc:`communication`
* :doc:`coredev`
    * :doc:`committing_hg_jy`
    * :doc:`committing`
    * :doc:`devcycle`
    * :doc:`buildbots_jy`
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

This guide is specifically for contributing to Jython, that is,
Python on the JVM. (While most of the standard library is written in Python,
the interpreter core is written in Java and integrates most easily with the Java
SE and EE ecosystems).

There are other Python implementations, each with a different focus.  Like
Jython, they always have more things they would like to do than they have
developers to work on them.  Some major example that may be of interest are:

* CPython_: The reference implementation of Python implemented in C,
  and the main focus of language development.
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
    * Jython's `Java coding standard`_
    * :PEP:`8` (Style Guide for Python Code)
* Source code
    * `Browse in GitHub <https://github.com/jython/jython/>`_

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
    * Information about editors and their configurations can be found in the
      `wiki <https://wiki.python.org/moin/PythonEditors>`_

* :ref:`Search this guide <search>`


Code of Conduct
---------------
Please note that all interactions on
`Python Software Foundation <https://www.python.org/psf-landing/>`__-supported
infrastructure is `covered
<https://www.python.org/psf/records/board/minutes/2014-01-06/#management-of-the-psfs-web-properties>`__
by the `PSF Code of Conduct <https://www.python.org/psf/codeofconduct/>`__,
which includes all infrastructure used in the development of Python itself
(e.g. mailing lists, issue trackers, GitHub, etc.).
In general this means everyone is expected to be open, considerate, and
respectful of others no matter what their position is within the project.

.. _contents:

Full Table of Contents
----------------------

.. toctree::
   :numbered:

   setup_jy
   help
   patch_hg_jy
   pullrequest
   runtests_jy
   coverage_jy
   docquality
   documenting
   silencewarnings
   fixingissues
   tracker
   triaging_jy
   communication
   coredev
   committing_hg_jy
   committing
   devcycle
   buildbots_jy
   stdlibchanges
   langchanges
   experts
   release_jy
   exploring_jy
   grammar_jy
   compiler_jy
   gitbootcamp
   faq_hg_jy

Specific to CPython_
--------------------

These are sections from the CPython guide, retained for reference.
A comparison with the CPython implementation of a feature will sometimes help
you understand the Jython one.

.. We're also keeping these files for the technical reason that changes from
   CPython upstream can only be merged if these CPython files continue to exist.

.. toctree::
   :maxdepth: 1

   setup
   runtests
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
.. _open pull requests: https://github.com/jython/jython/pulls?utf8=%E2%9C%93&q=is%3Apr%20is%3Aopen%20label%3A%22awaiting%20review%22
.. _Jython issue tracker: https://bugs.jython.org/
.. _Jython GitHub repository: https://github.com/jython/jython
.. _Java coding standard: https://wiki.python.org/jython/CodingStandards
.. _Style Guide for Java code: http://www.oracle.com/technetwork/java/codeconvtoc-136057.html
