.. Jython companion to grammar.rst

.. _grammar-jy:

Changing Jython's Grammar
=========================

.. warning:: At present, this is not much more than a copy of the CPython original
   with the obviously inapplicable crudely hacked out.

Abstract
--------

There's more to changing Python's grammar than editing
:file:` grammar/Python.g`.
For a start, we only do this in order to track a change developeed on
the reference implementation CPython.
This document aims to be a checklist of places that must also be fixed
when such a change is adopted by Jython.

It is probably incomplete.  If you see omissions,  submit a bug or patch.



Checklist
---------

