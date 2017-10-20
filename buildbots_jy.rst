.. Jython companion to buildbots.rst

.. _buildbots-jy:

Continuous Integration
======================

.. warning:: At present, this is not much more than a copy of the CPython original
   with the obviously inapplicable crudely hacked out.

.. note:: This description, while aimed at the future process, is applicable to
   PRs raised today, as far as test goes, but we merge the PR by a round-about
   route using Mercurial. It is also fair to point out that we have difficulty
   keeping these buildbots green.

To assert that there are no regressions in the :doc:`development and maintenance
branches <devcycle>`, Jython uses continuous integration services, integrated
into GitHub.
When a new commit appears in a PR on, all corresponding builders
will build and run the regression tests.

The build steps run by the buildbots are the following:

* Checkout of the source tree for the changeset which triggered the build
* Compiling Jython for a particular JVM
* Running the test suite
* Cleaning up

It is your responsibility, as a core developer, to check the automatic
build results as part of assessing a PR.  It is therefore
important that you get acquainted with the way these results are presented,
and how various kinds of failures can be explained and diagnosed.

Checking results of automatic builds
------------------------------------

The way to view recent build results is to go to the list of commits on GitHub
and click on the little red cross (or whatever the symbol is when a build
passes). In a PR-based process, the PR page itself contains a panel which shows
what tests would pass or fail if the PR were merged.

When several changes are committed in a quick succession in the same
branch, it often happens that only the latest is tested.


Stability
---------

At the time of writing, it is common for one or more build bots
to show failures that are due to configuration errors or transient failures.

The rule is that all stable builders must be free of
persistent failures when the release is cut.  It is absolutely **vital**
that core developers fix any issue they introduce on the buildbots,
as soon as possible.


Flags-dependent failures
------------------------

Sometimes, while you have run the :doc:`whole test suite <runtests_jy>` before
committing, you may witness unexpected failures on the buildbots.  One source
of such discrepancies is if different flags have been passed to the test runner
or to Python itself.  To reproduce, make sure you use the same flags as the
buildbots: they can be found out simply by clicking the **stdio** link for
the failing build's tests.  For example::

   ant regrtest

.. note:: Mention subtly different ant targets. Is this a problem?

.. note::
   Running ``Lib/test/regrtest.py`` is nearly equivalent to running
   ``-m test``.

Ordering-dependent failures
---------------------------

.. warning:: CPython content

Sometimes the failure is even subtler, as it relies on the order in which
the tests are run.  The buildbots *randomize* test order (by using the ``-r``
option to the test runner) to maximize the probability that potential
interferences between library modules are exercised; the downside is that it
can make for seemingly sporadic failures.

The ``--randseed`` option makes it easy to reproduce the exact randomization
used in a given build.  Again, open the ``stdio`` link for the failing test
run, and check the beginning of the test output proper.

Let's assume, for the sake of example, that the output starts with::

   ./python -Wd -E -bb Lib/test/regrtest.py -uall -rwW
   == CPython 3.3a0 (default:22ae2b002865, Mar 30 2011, 13:58:40) [GCC 4.4.5]
   ==   Linux-2.6.36-gentoo-r5-x86_64-AMD_Athlon-tm-_64_X2_Dual_Core_Processor_4400+-with-gentoo-1.12.14 little-endian
   ==   /home/buildbot/buildarea/3.x.ochtman-gentoo-amd64/build/build/test_python_29628
   Testing with flags: sys.flags(debug=0, inspect=0, interactive=0, optimize=0, dont_write_bytecode=0, no_user_site=0, no_site=0, ignore_environment=1, verbose=0, bytes_warning=2, quiet=0)
   Using random seed 2613169
   [  1/353] test_augassign
   [  2/353] test_functools

You can reproduce the exact same order using::

   ./python -Wd -E -bb -m test -uall -rwW --randseed 2613169

It will run the following sequence (trimmed for brevity)::

   [  1/353] test_augassign
   [  2/353] test_functools
   [  3/353] test_bool
   [  4/353] test_contains
   [  5/353] test_compileall
   [  6/353] test_unicode

If this is enough to reproduce the failure on your setup, you can then
bisect the test sequence to look for the specific interference causing the
failure.  Copy and paste the test sequence in a text file, then use the
``--fromfile`` (or ``-f``) option of the test runner to run the exact
sequence recorded in that text file::

   ./python -Wd -E -bb -m test -uall -rwW --fromfile mytestsequence.txt

In the example sequence above, if ``test_unicode`` had failed, you would
first test the following sequence::

   [  1/353] test_augassign
   [  2/353] test_functools
   [  3/353] test_bool
   [  6/353] test_unicode

And, if it succeeds, the following one instead (which, hopefully, shall
fail)::

   [  4/353] test_contains
   [  5/353] test_compileall
   [  6/353] test_unicode

Then, recursively, narrow down the search until you get a single pair of
tests which triggers the failure.  It is very rare that such an interference
involves more than **two** tests.  If this is the case, we can only wish you
good luck!

.. note::
   You cannot use the ``-j`` option (for parallel testing) when diagnosing
   ordering-dependent failures.  Using ``-j`` isolates each test in a
   pristine subprocess and, therefore, prevents you from reproducing any
   interference between tests.


Transient failures
------------------

While we try to make the test suite as reliable as possible, some tests do
not reach a perfect level of reproducibility.  Some of them will sometimes
display spurious failures, depending on various conditions.  Here are common
offenders:

* Network-related tests, such as ``test_poplib``, ``test_urllibnet``, etc.
  Their failures can stem from adverse network conditions, or imperfect
  thread synchronization in the test code, which often has to run a
  server in a separate thread.

* Tests dealing with delicate issues such as inter-thread or inter-process
  synchronization, or Unix signals: ``test_multiprocessing``,
  ``test_threading``, ``test_subprocess``, ``test_threadsignals``.

When you think a failure might be transient, it is recommended you confirm by
waiting for the next build.  Still, even if the failure does turn out sporadic
and unpredictable, the issue should be reported on the bug tracker; even
better if it can be diagnosed and suppressed by fixing the test's implementation,
or by making its parameters - such as a timeout - more robust.


