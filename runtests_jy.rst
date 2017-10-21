.. Jython companion to runtests.rst

.. _runtests-jy:

Running & Writing Tests (Jython)
================================

.. highlight:: console

.. note::

    This document assumes you are working from an
    :ref:`in-development <indevbranch>` checkout of Jython. If you
    are not then some things presented here may not work as they may depend
    on new features not available in earlier versions of Jython.

    The commands are given for Jython 2.7, and may be different in Jython 3.

Running
-------

The shortest, simplest way of running the test suite is the following command
from the root directory of your checkout::

   $ ant regrtest

This will run the majority of tests, but exclude a small portion of them;
these excluded tests are known failures on the current platform or
use special kinds of resources: for example, accessing the
Internet, or trying to play a sound or to display a graphical interface on
your desktop.  They are disabled by default so that running the test suite
is not too intrusive.  To enable some of these additional tests (and for
other flags which can help debug various issues such as reference leaks), read
the help text::

   $ dist/bin/jython -m test.regrtest -h

If you want to run a single test file, simply specify the test file name
(without the extension) as an argument.  You also probably want to enable
verbose mode (using ``-v``), so that individual failures are detailed::

   $ dist/bin/jython -m test.regrtest -v test_abc

This form may be used to run multiple tests by name,
and in a the order specified, or even repeat to tests::

   $ dist/bin/jython -m test.regrtest test_abc test_int test_abc

To run a single test case, use the ``unittest`` module, providing the import
path to the test case::

   $ dist/bin/jython -m unittest -v test.test_abc.TestABC


Unexpected Skips
^^^^^^^^^^^^^^^^

Sometimes when running the test suite, you will see "unexpected skips"
reported. These represent cases where an entire test module has been
skipped, but the test suite normally expects the tests in that module to
be executed on that platform.

Often, the cause is that an optional module hasn't been built due to missing
build dependencies. In these cases, the missing module reported when the test
is skipped should match one of the modules reported as failing to build when
:ref:`compiling-jy`.

In other cases, the skip message should provide enough detail to help figure
out and resolve the cause of the problem (for example, the default security
settings on some platforms will disallow some tests)


Writing
-------

Writing tests for Python is much like writing tests for your own code. Tests
need to be thorough, fast, isolated, consistently repeatable, and as simple as
possible. We try to have tests both for normal behaviour and for error
conditions.  Tests live in the ``Lib/test`` directory, where every file that
includes tests has a ``test_`` prefix.

One difference with ordinary testing is that you are encouraged to rely on the
:py:mod:`test.support` module. It contains various helpers that are tailored to
Python's test suite and help smooth out common problems such as platform
differences, resource consumption and cleanup, or warnings management.
That module is not suitable for use outside of the standard library.

When you are adding tests to an existing test file, it is also recommended
that you study the other tests in that file; it will teach you which precautions
you have to take to make your tests robust and portable.


Benchmarks
----------
Benchmarking is useful to test that a change does not degrade performance.

`The Python Benchmark Suite <https://github.com/python/performance>`_
has a collection of benchmarks for all Python implementations. Documentation
about running the benchmarks is in the `README.txt
<https://github.com/python/performance/blob/master/README.rst>`_ of the repo.
