.. Jython companion to coverage.rst

.. _coverage-jy:

Test Coverage in Jython
=======================

In broad groups, we have three things to test:

#. The Python Standard Library modules implemented in Python,
#. The Java implementation of modules in the Standard Library, and
#. The core of Jython implemented in Java.

In each case, Jython relies heavily on the tests provided by the CPython
reference implementation.

Jython adopts the Python Standard Library wholesale, and
its quality is assured by the CPython project.
Jython makes some small changes to its copy
to replace or add to tests and library modules.
We aim to minimise the Jython-specific code in both the
standard library and our tests.

Python development follows the practice that all semantic changes and additions
to the language and :abbr:`stdlib (standard library)` are accompanied by
appropriate unit tests.
This is the aim of ensuring sufficient :doc:`test coverage <coverage>`.
To the extent that CPython achieves this, a large part of Jython developement
may be driven by these freely provided tests.

Improvements to the library and its test coverage may emerge from Jython,
but the strategy depends on contributing them
:doc:`upstream to CPython <coverage>`.

.. _coverage-by-regrtest-jy:


Test Coverage Challenges
------------------------

There are places where Jython cannot depend on tests provided by CPython:

#. Java integration (calling and being called by Java).
#. Concurrency (where CPython's :abbr:`GIL (Global Interpreter Lock)` saves it
   but not Jython).
#. Tests dependent on garbage collection (sometimes inadvertently).
#. Tests that expose implementation details that differ.

For these cases we create tests in Python (to supersede or supplement the
ones in the CPython :mod:`test` module) and tests in Java.


Java Integration
''''''''''''''''

Java integration is unique to Jython, so we must have our own tests.
Where possible, tests should be in Python, using :mod:`unittest`.
Where tests have to be in Java, we recommend the JUnit_ framework.


Concurrency
'''''''''''

The JVM has strong support for concurrency.
There are many constructs in the Java library to support concurrency,
which may be used directly from Python.

Python also has support for concurrency, and a rich library,
but in the CPython interpreter,
only one thread may be executing CPython bytecode at a time.
Concurrency is limited to releasing the global lock during blocking operations
such as i/o.
If a task is CPU-bound,
one must use multiple CPython interpreters in order to scale it.

By contrast, the JVM may switch threads at any time,
part-way through what in CPython would be an atomic operation.
(JVM instructions may be atomic, but this barely helps us at all.)
Re-use or translation of implementations used in CPython needs to bear this
difference in mind, and additional tests are likely to be necessary to
assure us of correctness.

Some Jython applications will run in an environments with high concurrency,
and Java thread pools (such as a web-service),
where faulty concurrency is more likely to be exposed than in typical uses of
CPython.


Garbage Collection
''''''''''''''''''

Many older tests create objects they assume are garbage-collected immediately
they go out of scope.
Examples:

*  Files assumed closed as soon as dereferenced.
*  The timing of weak reference invalidation.

These tests may need adaptation to Java.
There is no way to force garbage collection in Java, only to "suggest" it.
Tests that open files are a particular problem on Windows,
where files that remain open when they pass out of scope,
cannot be deleted.


Jython Internals
''''''''''''''''

Tests of internal methods not accessible to Python are necessarily in Java,
usually as JUnit_ tests.
Examples:

*  The buffer interface.
*  Type exposure.


Filing the Issue
----------------

Once you have increased coverage, you need to create an issue on the
`Jython issue tracker`_ and a patch there.
(In the future process, you will probably submit a
:doc:`pull request <pullrequest>`.)
On the issue set the "Components" to "Test" and "Versions" to the version of
Jython you worked on (i.e., the in-development version).

.. _Jython issue tracker: http://bugs.jython.org
.. _Junit: http://junit.org

