.. This file is derived from a file of the same name in the CPython devguide
   and will receive updates from the CPython guide by merging.

.. _silencewarnings:

Silence Warnings From the Test Suite
====================================

.. warning:: At present, this is not much modified from the CPython base.

When running Python's test suite, no warnings should result when you run it
under :ref:`strenuous testing conditions <strenuous_testing>` (you can ignore
the extra flags passed to ``test`` that cause randomness and parallel execution
if you want). Unfortunately new warnings are added to Python on occasion which
take some time to eliminate (e.g., ``ResourceWarning``). Typically the easy
warnings are dealt with quickly, but the more difficult ones that require some
thought and work do not get fixed immediately.

If you decide to tackle a warning you have found, open an issue on the `issue
tracker`_ (if one has not already been opened) and say you are going to try and
tackle the issue, and then proceed to fix the issue.

.. _issue tracker: https://bugs.python.org
