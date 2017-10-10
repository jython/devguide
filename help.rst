.. This file is derived from a file of the same name in the CPython devguide
   and will receive updates from the CPython guide by merging.

.. _help:

Where to Get Help
=================

If you are working on Jython it is very possible you will come across an issue
where you need some assistance to solve it (this happens to core developers
all the time).

Should you require help, there are a :ref:`variety of options available
<communication>` to seek assistance. If the question involves process or tool
usage then please check the rest of this guide first as it should answer your
question.


Ask #jython
-----------

If you are comfortable with IRC you can try asking on ``#jython`` (on
the `freenode`_ network). Often there are experienced developers around,
ranging from triagers to core developers, who can answer questions about
developing for Jython, although it helps to be patient as it sometimes
takes a little while for a question to get noticed.

.. _freenode: http://freenode.net/


Core Mentorship
---------------

If you are interested in improving Jython and contributing to its development,
but don’t yet feel entirely comfortable with the public channels mentioned
above, `Python Mentors`_ are here to help you.  The Python Mentors list was
originally created to help with CPython development, but since Jython shares
most of the same infrastructure (Jython's repository is at hg.python.org
and uses a related Roundup issue tracker) it is a good place to find help for
Jython as well. Python is fortunate to have a community of volunteer core
developers willing to mentor anyone wishing to contribute code, work on bug
fixes or improve documentation.  Everyone is welcomed and encouraged to
contribute.

.. _Python Mentors: http://pythonmentors.com

.. FIXME: would Python Mentors count themselves a good place to go fro Jython?

Mailing Lists
-------------

Further options for seeking assistance include the `jython-users`_ and
`jython-dev`_ mailing lists.  Jython-users contains discussion of the use of
Jython in development.  Jython-dev contains discussion of current Jython design
issues, release mechanics, and maintenance of existing releases.  Just remember
that ``jython-dev`` is for questions involving the development *of* Jython
whereas ``jython-users`` is for questions concerning development *with* Jython.


File a Bug
----------

If you strongly suspect you have stumbled on a bug (be it in the build
process, in the test suite, or in other areas), then open an issue on the
`Jython issue tracker`_.  As with every bug report it is strongly advised that
you detail which conditions triggered it (including the OS name and version,
which JVM and version, and what you were trying to do), as well as the exact
error message you encountered.

.. _Jython issue tracker: http://bugs.jython.org

.. _jython-users: https://lists.sourceforge.net/lists/listinfo/jython-users
.. _jython-dev: https://lists.sourceforge.net/lists/listinfo/jython-dev
