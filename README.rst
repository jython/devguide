The Jython Developer's Guide
============================

.. This will be wrong now, but we need something similar:
   image:: https://readthedocs.org/projects/python-devguide/badge/
   :target: https://devguide.python.org
   :alt: Documentation Status

This guide is a straight copy of the CPython Developer's Guide, with
the idea of adapting it to describe Jython development processes
using GitHub.
The difference is made by adding files for chapters that need to be
different from the CPython edition,
keeping the disused CPython files,
and changing other files as little as possible.
That way may continue to pull changes from CPython's devguide.

The official home of this guide is still to be chosen.

Compilation
-----------

For the compilation of the devguide, you need to use a version of Python which
supports the ``venv`` module, because the ``make html`` command will create a
virtual environment and will install the ``Sphinx`` package::

    make html

