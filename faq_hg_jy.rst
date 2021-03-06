.. Jython and Mercurial-specific file included to satisfy some references,
   derived from the former CPython devguide file raq.rst

:tocdepth: 2

.. _faq:

Jython Developer FAQ
~~~~~~~~~~~~~~~~~~~~

.. note::

   The "frequently asked questions" in the CPython devguide were redistributed
   to the sections of the guide that deal with the subject of the question,
   under the a PR to `delete the FAQ`_.
   In the process we lose the Jython and Mercurial answers,
   so we're keeping it in the Jython version for the time being.

.. _delete the FAQ: https://github.com/python/devguide/issues/27

.. contents::
   :local:

Communications
==============


Where should I ask general Jython questions?
--------------------------------------------

General Jython questions should still go to `jython-users`_ or `tutor`_
or similar resources, such as StackOverflow_ or the ``#jython`` IRC channel
on Freenode_.

.. _jython-users: https://lists.sourceforge.net/lists/listinfo/jython-users
.. _tutor: http://mail.python.org/mailman/listinfo/tutor
.. _StackOverflow: http://stackoverflow.com/
.. _Freenode: http://freenode.net/


.. index::
   single: PEP process; in FAQ

.. _suggesting-changes-faq:

Where should I suggest new features and language changes?
---------------------------------------------------------

The `python-ideas`_ mailing list is specifically intended for discussion of
new features and language changes. Please don't be disappointed if your
idea isn't met with universal approval: as the long list of Rejected and
Withdrawn PEPs in the `PEP Index`_ attests, and as befits a reasonably mature
programming language, getting significant changes into Python isn't a simple
task.

If the idea is reasonable, someone will suggest posting it as a feature
request on the `issue tracker`_, or, for larger changes, writing it up as
a `draft PEP`_.

Sometimes core developers will differ in opinion, or merely be collectively
unconvinced. When there isn't an obvious victor then the
`Status Quo Wins a Stalemate`_ as outlined in the linked post.

For some examples on language changes that were accepted please read
`Justifying Python Language Changes`_.

See also the :ref:`langchanges` section of this guide.

.. _python-ideas: http://mail.python.org/mailman/listinfo/python-ideas
.. _issue tracker: http://bugs.python.org
.. _PEP Index: http://www.python.org/dev/peps/
.. _draft PEP: http://www.python.org/dev/peps/pep-0001/
.. _Status Quo Wins a Stalemate: http://www.curiousefficiency.org/posts/2011/02/status-quo-wins-stalemate.html
.. _Justifying Python Language Changes: http://www.curiousefficiency.org/posts/2011/02/justifying-python-language-changes.html

Where should I ask general questions about contributing to Jython?
------------------------------------------------------------------

Jython development is generally discussed on the jython-dev_ list.
There is also the `Python Mentors`_ program which is specifically about
encouraging developers and others that would like to contribute to Python and
Jython development in general, rather than necessarily being focused on one
particular issue. Some core developers are also available on the ``#jython``
IRC channel on Freenode_.

.. _jython-dev: https://lists.sourceforge.net/lists/listinfo/jython-dev
.. _Python Mentors: http://pythonmentors.com


Where should I report specific problems?
----------------------------------------

Specific problems should be posted to the `Jython issue tracker`_.

.. _Jython issue tracker: http://bugs.jython.org

What if I'm not sure it is a bug?
---------------------------------

The general Python help locations listed above are the best place to start
with that kind of question. If they agree it looks like a bug, then the
next step is to either post it to the `Jython issue tracker`_ or else to ask further
on the core development mailing list, `jython-dev`_.

.. _jython-dev: https://lists.sourceforge.net/lists/listinfo/jython-dev


What if I disagree with an issue resolution on the tracker?
-----------------------------------------------------------

First, take some time to consider any comments made in association with the
resolution of the tracker issue. On reflection, they may seem more reasonable
than they first appeared.

If you still feel the resolution is incorrect, then raise the question on
`jython-dev`_.

.. _jython-dev: https://lists.sourceforge.net/lists/listinfo/jython-dev

How do I tell who is and isn't a core developer?
------------------------------------------------

As Jython core developers are on the same list as CPython core developers, you
can check their name against the `full list of developers`_ with commit
rights to the main source control repository.

On the `Jython issue tracker`_, most core developers will have the Python logo
appear next to their name. (This is not yet true on the Jython tracker, we're
working on it).

.. _full list of developers: http://hg.python.org/committers.txt


What standards of behaviour are expected in these communication channels?
-------------------------------------------------------------------------

We try to foster environments of mutual respect, tolerance and encouragement,
as described in the PSF's `Diversity Statement`_. Abiding by the guidelines
in this document and asking questions or posting suggestions in the
appropriate channels are an excellent way to get started on the mutual respect
part, greatly increasing the chances of receiving tolerance and encouragement
in return.

.. _Diversity Statement: http://www.python.org/psf/diversity/


Version Control
===============

For everyone
------------

The following FAQs are intended for both core developers and contributors.

Where can I learn about the version control system used, Mercurial (hg)?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Mercurial_'s (also known as ``hg``) official web site is at
http://mercurial.selenic.com/.  A book on Mercurial published by
`O'Reilly Media`_, `Mercurial: The Definitive Guide`_, is available
for free online.  Another resource is `Hg Init: a Mercurial tutorial`_
by Joel Spolsky.

With Mercurial installed, you can run the help tool that comes with
Mercurial to get help::

  hg help

The `man page`_ for ``hg`` provides a quick refresher on the details of
various commands, but doesn't provide any guidance on overall
workflow.

.. _Mercurial: http://mercurial.selenic.com/
.. _O'Reilly Media: http://www.oreilly.com/
.. _Mercurial\: The Definitive Guide: http://hgbook.red-bean.com/
.. _man page: http://www.selenic.com/mercurial/hg.1.html
.. _Hg Init\: a Mercurial tutorial: http://hginit.com/


I already know how to use Git, can I use that instead?
''''''''''''''''''''''''''''''''''''''''''''''''''''''

.. warning:: Specific to CPython

While the main workflow for core developers requires Mercurial, if
you just want to generate patches with ``git diff`` and post them to the
`issue tracker`_, Petri Lehtinen maintains a `git mirror`_ of the main
`CPython repository`_. To create a local clone based on this mirror rather
than the main repository::

    git clone git://github.com/akheron/cpython

The mirror's master branch tracks the main repository's default branch,
while the maintenance branch names (``2.7``, ``3.3``, etc) are mapped
directly.

.. _git mirror: http://github.com/akheron/cpython
.. _CPython repository: http://hg.python.org/cpython

Please only use this approach if you're already an experienced Git user and
don't require assistance with the specifics of version control commands. All
other parts of this developer's guide assume the use of Mercurial for local
version control.


What do I need to use Mercurial?
''''''''''''''''''''''''''''''''

UNIX
^^^^

First, you need to `download Mercurial`_.  Most UNIX-based operating systems
have binary packages available.  Most package management systems also
have native Mercurial packages available.

If you have push rights, you need OpenSSH_.  This is needed to verify
your identity when performing commits. As with Mercurial, binary packages
are typically available either online or through the platform's package
management system.

Mercurial does not use its own compression via SSH
because it is better to enable compression at the SSH level.  Enabling
SSH compression can make cloning a remote repository much faster.
You can configure it in your ``~/.ssh/config`` file; for example::

   Host hg.python.org
     Compression yes

.. _download Mercurial: http://mercurial.selenic.com/downloads
.. _OpenSSH: http://www.openssh.org/


Windows
^^^^^^^

The recommended option on Windows is to `download TortoiseHg`_ which
integrates with Windows Explorer and also bundles the command line client
(meaning you can type ``hg`` in a DOS box).  Note that most
entries in this FAQ only cover the command line client in detail - refer
to the TortoiseHg documentation for assistance with its graphical interface.

If you have push rights, you need to configure Mercurial to work with
your SSH keys.  For that, open your Mercurial configuration file
(you can do so by opening the TortoiseHg Global Settings dialog and then
clicking *"Edit File"*).  If there is no ``[ui]`` section, create it by
typing just that on a line by itself. Then add the following line::

   ssh = TortoisePlink.exe -ssh -2 -C -i C:\path\to\yourkey.ppk

where ``C:\path\to\yourkey.ppk`` should be replaced with the actual path
to your SSH private key.

.. note::
   If your private key is in OpenSSH format, you must first convert it to
   PuTTY format by loading it into `PuTTYgen`_.

.. _download TortoiseHg: http://tortoisehg.bitbucket.org/download/index.html


What's a working copy? What's a repository?
'''''''''''''''''''''''''''''''''''''''''''

Mercurial is a "distributed" version control system.  This means that each
participant, even casual contributors, download a complete copy (called a
*clone*, since it is obtained by calling ``hg clone``) of the central
repository which can be treated as a stand-alone repository for all purposes.
That copy is called in the FAQ the *local repository*, to differentiate
with any *remote repository* you might also interact with.

But you don't modify files directly in the local repository; Mercurial doesn't
allow for it.  You modify files in what's called the *working copy* associated
with your local repository: you also run compilations and tests there.
Once you are satisfied with your changes, you can :ref:`commit them <hg-commit>`;
committing records the changes as a new *revision* in the *local repository*.

Changes in your *local repository* don't get automatically shared with the
rest of the world.  Mercurial ensures that you have to do so explicitly
(this allows you to experiment quite freely with multiple branches of
development, all on your private computer).  The main commands for doing
so are ``hg pull`` and ``hg push``.


Which branches are in my local repository?
''''''''''''''''''''''''''''''''''''''''''

Typing ``hg branches`` displays the open branches in your local repository::

  $ hg branches
  default                     7083:358986e4d053
  2.5                         7065:5ce837b1a1d8
  2.2                         6412:bed9f9de4ef3

.. _hg-current-branch:

Which branch is currently checked out in my working copy?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Use::

   $ hg branch
   default

Or to get more information::

   $ hg summary
   parent: 7083:358986e4d053 tip
    Merge forward.
    branch: default
    commit: 17 unknown (clean)
    update: (current)


.. _hg-switch-branches:

How do I switch between branches inside my working copy?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Simply use ``hg update`` to checkout another branch in the current directory::

   $ hg branch
   default
   $ hg update 3.3
   86 files updated, 0 files merged, 11 files removed, 0 files unresolved
   $ hg branch
   3.3

Adding the ``-v`` option to ``hg update`` will list all updated files.


I want to keep a separate working copy per development branch, is it possible?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

There are two ways:

1) Use the "`share extension`_" as described in the :ref:`multiple-clones`
   section;
2) Create several clones of your local repository;

If you want to use the second way, you can do::

   $ hg clone jython jy33
   updating to branch default
   3434 files updated, 0 files merged, 0 files removed, 0 files unresolved
   $ cd jy33
   $ hg update 3.3
   86 files updated, 0 files merged, 11 files removed, 0 files unresolved

The current branch in a working copy is "sticky": if you pull in some new
changes, ``hg update`` will update to the head of the *current branch*.

.. _share extension: http://mercurial.selenic.com/wiki/ShareExtension


.. _hg-paths:

How do I link my local repository to a particular remote repository?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Your local repository is linked by default to the remote repository it
was *cloned* from.  If you created it from scratch, however, it is not linked
to any remote repository.  In ``.hg/hgrc`` file for the local repository, add
or modify the following section::

  [paths]
  default = ssh://hg@hg.python.org/jython-docs/devguide

This example is for a local repository that mirrors the ``devguide`` repository
on ``hg.python.org``. The same approach works for other remote repositories.

Anywhere that ``<remote repository>`` is used in the commands in this
FAQ, ``hg`` will use the default remote repository if you omit the parameter.


How do I create a shorthand alias for a remote repository?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

In your global ``.hgrc`` file add a section similar to the following::

  [paths]
  dg = ssh://hg@hg.python.org/jython-docs/devguide

This example creates a ``dg`` alias for this ``devguide`` repository
on ``hg.python.org``. This allows "dg" to be entered instead of the
full URL for commands taking a repository argument (e.g. ``hg pull dg`` or
``hg outgoing dg``).

Anywhere that ``<remote repository>`` is used in the commands in this
FAQ, ``hg`` should accept an alias in place of a complete remote URL.


How do I compare my local repository to a remote repository?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

To display the list of changes that are in your local repository, but not
in the remote, use::

 hg outgoing <remote repository>

This is the list of changes that will be sent if you call
``hg push <remote repository>``.  It does **not** include any :ref:`uncommitted
changes <hg-status>` in your working copy!

Conversely, for the list of changes that are in the remote repository but
not in the local, use::

 hg incoming <remote repository>

This is the list of changes that will be retrieved if you call
``hg pull <remote repository>``.

.. note::
   In most daily use, you will work against the default remote repository,
   and therefore simply type ``hg outgoing`` and ``hg incoming``.

   In this case, you can also get a synthetic summary using
   ``hg summary --remote``.


How do I update my local repository to be in sync with a remote repository?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Run::

   hg pull <remote repository>

from the repository you wish to pull the latest changes into.  Most of the
time, that repository is a clone of the repository you want to pull from,
so you can simply type::

   hg pull

This doesn't update your working copy, though.  See below:


How do I update my working copy with the latest changes?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Do::

   hg update

This will update your working copy with the latest changes on the
:ref:`current branch <hg-current-branch>`.  If you had :ref:`uncommitted
changes <hg-status>` in your working copy, they will be merged in.

If you find yourself typing often ``hg pull`` followed by ``hg update``,
be aware that you can combine them in a single command::

   hg pull -u


.. _hg-local-workflow:

How do I apply a patch?
'''''''''''''''''''''''

If you want to try out or review a patch generated using Mercurial, do::

   patch -p1 < somework.patch

This will apply the changes in your working copy without committing them.
If the patch was not created by Mercurial (for example, a patch created by
Subversion and thus lacking any ``a``/``b`` directory prefixes in the patch),
replace ``-p1`` with ``-p0``.

If the patch contains renames, deletions or copies, and you intend committing
it after your review, you might prefer using::

   hg import --no-commit somework.patch

If you want to work on the patch using mq_ (Mercurial Queues), type instead::

   hg qimport somework.patch

This will create a patch in your queue with a name that matches the filename.
You can use the ``-n`` argument to specify a different name.  To have the
patch applied to the working copy, type::

   hg qpush

Finally, to delete the patch, first un-apply it if necessary using ``hg qpop``,
then do::

   hg qdelete somework.patch

.. _extended diff format: http://www.selenic.com/mercurial/hg.1.html#diffs
.. _mq: http://mercurial.selenic.com/wiki/MqExtension


.. _merge-patch:

How do I solve conflicts when applying a patch fails?
'''''''''''''''''''''''''''''''''''''''''''''''''''''

The standard ``patch`` command, as well as ``hg import``, will produce
unhelpful ``*.rej`` files when it fails applying parts of a patch.
We suggest you try the mpatch_ utility, which can help resolve a number of
common causes of patch rejects.

To make use of ``mpatch`` transparent, you can define a shell alias in one
of your startup files.  For example, if you want it to open the ``kdiff3``
merge program to fix failing patch hunks::

   alias patch='mpatch --merge=kdiff3'

or if you want it to automatically solve conflicts by using heuristics::

   alias patch='mpatch --auto --no-merge'

.. _mpatch: http://oss.oracle.com/~mason/mpatch/


How do I add a file or directory to the repository?
'''''''''''''''''''''''''''''''''''''''''''''''''''

Simply specify the path to the file or directory to add and run::

 hg add PATH

If ``PATH`` is a directory, Mercurial will recursively add any files in that
directory and its descendants.

If you want Mercurial to figure out by itself which files should be added
and/or removed, just run::

 hg addremove

**Be careful** though, as it might add some files that are not desired in
the repository (such as build products, cache files, or other data).

You will then need to run ``hg commit`` (as discussed below) to commit
the file(s) to your local repository.


What's the best way to split a file into several files?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''

To split a file into several files (e.g. a module converted to a package or a
long doc file divided in two separate documents) use ``hg copy``::

    hg copy module.rst module2.rst

and then remove the parts that are not necessary from ``module.rst`` and
``module2.rst``.  This allows Mercurial to know that the content of
``module2.rst`` used to be in ``module.rst``, and will make subsequent merges
easier.  If necessary, you can also use ``hg copy`` several times.

If you simply create ``module2.rst``, add it with ``hg add``, and copy part of
the content from ``module.rst``, Mercurial won't know that the two file are
related.


How do I delete a file or directory in the repository?
''''''''''''''''''''''''''''''''''''''''''''''''''''''

Specify the path to be removed with::

 hg remove PATH

This will remove the file or the directory from your working copy; you will
have to :ref:`commit your changes <hg-commit>` for the removal to be recorded
in your local repository.


.. _hg-status:

What files are modified in my working copy?
'''''''''''''''''''''''''''''''''''''''''''

Running::

 hg status

will list any pending changes in the working copy.  These changes will get
committed to the local repository if you issue an ``hg commit`` without
specifying any path.

Some
key indicators that can appear in the first column of output are:

   =  ===========================
   A  Scheduled to be added
   R  Scheduled to be removed
   M  Modified locally
   ?  Not under version control
   =  ===========================

If you want a line-by-line listing of the differences, use::

 hg diff


How do I revert a file I have modified back to the version in the repository?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

Running::

 hg revert PATH

will revert ``PATH`` to its version in the repository, throwing away any
changes you made locally.  If you run::

 hg revert -a

from the root of your working copy it will recursively restore everything
to match up with the repository.


How do I find out who edited or what revision changed a line last?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

You want::

 hg annotate PATH

This will output to stdout every line of the file along with which revision
last modified that line.  When you have the revision number, it is then
easy to :ref:`display it in detail <hg-log-rev>`.


.. _hg-log:

How can I see a list of log messages for a file or specific revision?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

To see the history of changes for a specific file, run::

 hg log -v [PATH]

That will list all messages of revisions which modified the file specified
in ``PATH``.  If ``PATH`` is omitted, all revisions are listed.

If you want to display line-by-line differences for each revision as well,
add the ``-p`` option::

 hg log -vp [PATH]

.. _hg-log-rev:

If you want to view the differences for a specific revision, run::

 hg log -vp -r <revision number>


How can I see the changeset graph in my repository?
'''''''''''''''''''''''''''''''''''''''''''''''''''

In Mercurial repositories, changesets don't form a simple list, but rather
a graph: every changeset has one or two parents (it's called a merge changeset
in the latter case), and can have any number of children.

The graphlog_ extension is very useful for examining the structure of the
changeset graph.  It is bundled with Mercurial.

Graphical tools, such as TortoiseHG, will display the changeset graph
by default.

.. _graphlog: http://mercurial.selenic.com/wiki/GraphlogExtension


How do I update to a specific release tag?
''''''''''''''''''''''''''''''''''''''''''

Run::

   hg tags

to get a list of tags.  To update your working copy to a specific tag, use::

   hg update <tag>


How do I find which changeset introduced a bug or regression?
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

``hg bisect``, as the name indicates, helps you do a bisection of a range of
changesets.

You need two changesets to start the search: one that is "good"
(doesn't have the bug), and one that is "bad" (has the bug).  Usually, you
have just noticed the bug in your working copy, so you can start with::

   hg bisect --bad

Then you must ``update`` to a previous changeset that doesn't have the bug.
You can conveniently choose a faraway changeset (for example a former release),
and check that it is indeed "good".  Then type::

   hg bisect --good

Mercurial will automatically bisect so as to narrow the range of possible
culprits, until a single changeset is isolated.  Each time Mercurial presents
you with a new changeset, re-compile Python and run the offending test, for
example::

   make -j2
   ./python -m test -uall test_sometest

Then, type either ``hg bisect --good`` or ``hg bisect --bad`` depending on
whether the test succeeded or failed.


How come feature XYZ isn't available in Mercurial?
''''''''''''''''''''''''''''''''''''''''''''''''''

Mercurial comes with many bundled extensions which can be explicitly enabled.
You can get a list of them by typing ``hg help extensions``.  Some of these
extensions, such as ``color``, can prettify output; others, such as ``fetch``
or ``graphlog``, add new Mercurial commands.

There are also many `configuration options`_ to tweak various aspects of the
command line and other Mercurial behaviour; typing `man hgrc`_ displays
their documentation inside your terminal.

In the end, please refer to the Mercurial `wiki`_, especially the pages about
`extensions`_ (including third-party ones) and the `tips and tricks`_.


.. _man hgrc: http://www.selenic.com/mercurial/hgrc.5.html
.. _wiki: http://mercurial.selenic.com/wiki/
.. _extensions: http://mercurial.selenic.com/wiki/UsingExtensions
.. _tips and tricks: http://mercurial.selenic.com/wiki/TipsAndTricks
.. _configuration options: http://www.selenic.com/mercurial/hgrc.5.html


.. _core-devs-faqs:

For core developers
-------------------

These FAQs are intended mainly for core developers.


.. _hg-commit:

How do I commit a change to a file?
'''''''''''''''''''''''''''''''''''

To commit any changes to a file (which includes adding a new file or deleting
an existing one), you use the command::

 hg commit [PATH]

``PATH`` is optional: if it is omitted, all changes in your working copy
will be committed to the local repository.  When you commit, be sure that all
changes are desired by :ref:`reviewing them first <hg-status>`;
also, when making commits that you intend to push to public repositories,
you should **not** commit together unrelated changes.

To abort a commit that you are in the middle of, leave the message
empty (i.e., close the text editor without adding any text for the
message).  Mercurial will then abort the commit operation so that you can
try again later.

Once a change is committed to your local repository, it is still only visible
by you.  This means you are free to experiment with as many local commits
you feel like.

.. note::
   If you do not like the default text editor Mercurial uses for
   entering commit messages, you may specify a different editor,
   either by changing the ``EDITOR`` environment variable or by setting
   a Mercurial-specific editor in your global ``.hgrc`` with the ``editor``
   option in the ``[ui]`` section.


.. _hg-merge-conflicts:

How do I solve merge conflicts?
'''''''''''''''''''''''''''''''

The easiest way is to install KDiff3 --- Mercurial will open it automatically
in case of conflicts, and you can then use it to solve the conflicts and
save the resulting file(s).  KDiff3 will also take care of marking the
conflicts as resolved.

If you don't use a merge tool, you can use ``hg resolve --list`` to list the
conflicting files, resolve the conflicts manually, and the use
``hg resolve --mark <file path>`` to mark these conflicts as resolved.
You can also use ``hg resolve -am`` to mark all the conflicts as resolved.

.. note::
   Mercurial will use KDiff3 automatically if it's installed and it can find
   it --- you don't need to change any settings.  KDiff3 is also already
   included in the installer of TortoiseHg.  For more information, see
   http://mercurial.selenic.com/wiki/KDiff3.


.. _hg-null-merge:

How do I make a null merge?
'''''''''''''''''''''''''''

If you committed something (e.g. on 3.3) that shouldn't be ported on newer
branches (e.g. on default), you have to do a *null merge*::

   cd 3.x
   hg merge 3.3
   hg revert -ar default
   hg resolve -am  # needed only if the merge created conflicts
   hg ci -m '#12345: null merge with 3.3.'

Before committing, ``hg status`` should list all the merged files as ``M``,
but ``hg diff`` should produce no output.  This will record the merge without
actually changing the content of the files.


.. _hg-heads-merge:

I got "abort: push creates new remote heads!" while pushing, what do I do?
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

If you see this message while pushing, it means that you committed something
on a clone that was not up to date, thus creating a new head.
This usually happens for two reasons:

1. You forgot to run ``hg pull`` and/or ``hg up`` before committing;
2. Someone else pushed on the main repo just before you, causing a push race;

First of all you should pull the new changesets using ``hg pull``.  Then you can
use ``hg heads`` to see which branches have multiple heads.

If only one branch has multiple heads, you can do::

   cd default
   hg heads .
   hg up csid-of-the-other-head
   hg merge
   hg ci -m 'Merge heads.'

``hg heads .``  will show you the two heads of the current branch: the one you
pulled and the one you created with your commit (you can also specify a branch
with ``hg heads <branch>``).  While not strictly necessary, it is highly
recommended to switch to the other head before merging.  This way you will be
merging only your changeset with the rest, and in case of conflicts it will be
a lot easier.

If more than one branch has multiple heads, you have to repeat these steps for
each branch.  Since this creates new changesets, you will also have to
:ref:`merge them between branches <branch-merge-hg-jy>`.
For example, if both ``3.3``
and ``default`` have multiple heads, you should first merge heads in ``3.3``,
then merge heads in ``default``, and finally merge ``3.3`` with ``default``
using ``hg merge 3.3`` as usual.

In order to avoid this, you should *always remember to pull and update before
committing*.


How do I undo the changes made in a recent commit?
''''''''''''''''''''''''''''''''''''''''''''''''''

First, this should not happen if you take the habit of :ref:`reviewing changes
<hg-status>` before committing them.

In any case, run::

 hg backout <revision number>

This will modify your working copy so that all changes in ``<revision number>``
(including added or deleted files) are undone.  You then need to :ref:`commit
<hg-commit>` these changes so that the backout gets permanently recorded.

.. note::
   These instructions are for Mercurial 1.7 and higher.  ``hg backout`` has
   a slightly different behaviour in versions before 1.7.


SSH
===

How do I generate an SSH 2 public key?
--------------------------------------

All generated SSH keys should be sent to hgaccounts@python.org for
adding to the list of keys.

UNIX
''''

Run::

  ssh-keygen -t rsa

This will generate two files; your public key and your private key.  Your
public key is the file ending in ``.pub``.

Windows
'''''''

Use PuTTYgen_ to generate your public key.  Choose the "SSH2 DSA" radio button,
have it create an OpenSSH formatted key, choose a password, and save the private
key to a file.  Copy the section with the public key (using Alt-P) to a file;
that file now has your public key.

.. _PuTTYgen: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


Is there a way to avoid having to constantly enter my password for my SSH 2 public key?
---------------------------------------------------------------------------------------

UNIX
''''

Use ``ssh-agent`` and ``ssh-add`` to register your private key with SSH for
your current session.  The simplest solution, though, is to use KeyChain_,
which is a shell script that will handle ``ssh-agent`` and ``ssh-add`` for you
once per login instead of per session.

.. _KeyChain: http://www.gentoo.org/proj/en/keychain/


.. _pageant:

Windows
'''''''

The Pageant program is bundled with TortoiseHg.  You can find it in its
installation directory (usually ``C:\Program Files (x86)\TortoiseHg\``);
you can also `download it separately
<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html>`_.

Running Pageant will prevent you from having to type your password constantly.
If you add a shortcut to Pageant to your Autostart group and edit the shortcut
so that the command line includes an argument to your private key then Pageant
will load the key every time you log in.


Can I make commits from machines other than the one I generated the keys on?
----------------------------------------------------------------------------

You can :ref:`make commits <hg-commit>` from any machine, since they will be
recorded in your *local repository*.

However, to push these changes to the remote server, you will need proper
credentials.  All you need is to make sure that the machine you want to
push changes from has both the public and private keys in the standard
place that ssh will look for them (i.e. ~/.ssh on Unix machines).
Please note that although the key file ending in .pub contains your
user name and machine name in it, that information is not used by the
verification process, therefore these key files can be moved to a
different computer and used for verification.  Please guard your keys
and never share your private key with anyone.  If you lose the media
on which your keys are stored or the machine on which your keys are
stored, be sure to report this to pydotorg@python.org at the same time
that you change your keys.
