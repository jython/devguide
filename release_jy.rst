=====================
How To Release Jython
=====================

These are the steps needed to make a public release of Jython.
In the case of a public release,
you can only do this once the hard work of development and debugging is done,
and there is a consensus in the project that adequate quality has been achieved.

These notes will also be a useful guide if you intend to make a snapshot (private) release.
The process may be tested, without making a real release,
for example to check that the scripts still work:
go through the steps but without pushing upstream or publishing.
Delete the workspace soon afterwards,
so as not to leave change sets tagged as a release,
that might be accidentally built upon or pushed at a later time.

To complete a public release you need the following things:

* Two JDBC driver JARs that we do not track with version control (licensing restrictions):

  * Informix (currently ``jdbc-4.50.1.jar`` for Java 8).
  * Oracle (currently ``ojdbc8.jar`` for Java 8).

.. Padding. See https://github.com/sphinx-doc/sphinx/issues/2258

* Commit rights to the Jython repository (to push the tagged version).
* The right to publish Jython at Sonatype_.
* A PGP signing key pair (generated with ``gpg --gen-key``).
* Access to the channels where we announce releases (e.g. Twitter).
* Access to modify the bug-tracker configuration.

You can dry-run this process with only the first pre-requisite (driver JARs),
and a Mercurial clone of the official repository.
In that case, be careful not to push any changes.
(Hint: if you clone from ``https://github.com/jython/jython.git``,
that will prevent an unintended push.)

.. _Sonatype: https://oss.sonatype.org


Making a Releasable Jython
==========================

Start in the Right Place
------------------------

``cd`` to a directory where you have permission to make sub-directories.
The path to this directory can get embedded in published files,
so aim for:

* short (i.e. near a file system root), in the examples ``D:\git``.
* impersonal (not containing company or personal names).
* ASCII (even though Jython is pretty good with Unicode now).

The examples in this text were mostly made in Windows PowerShell,
but Git remote operations are in Git Bash.


Clone the Repository
--------------------

Clone the repository to a named sub-directory and ``cd`` into it:

..  code-block:: bash

    $ git clone git@github.com:jeff5/jython-nightjar.git work
    Cloning into 'work'...
    $ cd work
    $ git describe --all
    heads/master


Tool Check
----------

We must build with the right version of Java.
(At the time of writing we target Java 8.)
At the same time, let's check that we have the tools we need on the path:

.. code-block:: ps1con

    PS git> java -version
    java version "1.8.0_241"
    PS git> ant -version
    Apache Ant(TM) version 1.9.14 compiled on March 12 2019
    PS git> gpg --version
    gpg (GnuPG) 2.2.17
    libgcrypt 1.8.4
    PS git> git --version
    git version 2.26.2.windows.1
    PS git> cd work
    PS work> git log --oneline --graph  -3
    * ba16fbc48 (HEAD -> master, origin/master, origin/HEAD) Swap from hg to git as our SCM tool.
    * ed4965e5e Begin to identify as Jython 2.7.3a1.
    * faf935172 Added tag v2.7.2 for changeset 925a3cc3b49d


.. _changes-preparing-for-a-release:

Changes Preparing for a Release
-------------------------------

The following files may need to be updated to match the version you are about to release:

* ``build.xml``: The version number appears piece by piece in the target ``common-config``.
  Update these properties:

  * ``jython.major_version``,
  * ``jython.minor_version``,
  * ``jython.micro_version``,
  * ``jython.release_level``, and
  * ``jython.release_serial``.

  In the language of these properties,
  version 2.7.3a1 is spelled ``2``, ``7``, ``3``, ``${PY_RELEASE_LEVEL_ALPHA}``, ``1``.
  Every other expression needing a version number is derived from these 5 values.
* ``build.gradle``: The version number appears as a simple string property ``version``,
  near the top of the file.
  Version 2.7.3a1 is simply set like this: ``version = '2.7.3a1'``.
* ``src/org/python/core/imp.java``: If there has been any compiler change,
  increment the magic number ``APIVersion``.
  This magic declares old compiled files incompatible, forcing a fresh compilation for users.
  (Maybe do it anyway, if it's been a long time.)
* ``README.txt``: It is possible no change is needed at all,
  and if a change is needed, it will probably only be to the running text.
  A copy of this file is made during the build,
  in which information from ``build.xml`` replaces the place-holders.
  (The place-holders look like ``@jython.version@``, etc..)
  The resulting text is what a user sees when installing interactively.
  It automatically includes a prominent banner when making a snapshot build.
* ``NEWS``: First try to ensure we have listed all issues closed since the last release.
  The top of this file may look like:

  ..  code-block:: text

      Jython <something>
        Bugs fixed
          - [ NNNN ] ...

  Replace the first line with the release you are building e,g, "Jython 2.7.3a1".
  Add anything necessary to the section "New Features".
  After publication (not now),
  we will add a new, empty, section for the version then under development.

These version-settings may already have been made correctly,
to match the identity of the next release.
The build script ensures that, until we actually tag a change set as a release,
the version numbers set here will always appear with a "snapshot" suffix.

You should run the ``ant javatest`` and ``ant regrtest`` targets at this point.
These should run clean, or at least failures be explained and acceptable,
e.g. known to be attributable to limitations in your network environment.
If bugs are discovered that you need to fix,
it would be best to abandon work on this repository and
fix them in your usual development workbench.

..  note:: You can run the ``ant bugtest`` target, but it is deprecated.
    (We haven't maintained it as Jython changed.)
    It produces some failures known to be spurious.
    It also creates files you have to clean up manually before you can build for a release.

If you changed anything, commit this set of changes locally:

..  code-block:: ps1con

    PS work> git commit -m"Prepare for 2.7.3a1 release."


Get the JARs
------------

Find the database driver JARs from reputable sources.

* The Informix driver may be obtained from Maven Central.
  Version ``jdbc-4.50.1.jar`` is known to work on Java 8.

* The Oracle JDBC driver may be found at ``download.oracle.com``.
  An account is required, the same one you use to update your JDK.
  (The JARs on Maven Central seem to be unofficial postings.)
  For Java 8 use ``ojdbc8.jar``.

Let's assume we put the JARs in ``D:\git\support``.
Create an ``ant.properties`` correspondingly:

..  code-block:: properties

    # Ant properties defined externally to the release build.
    informix.jar = D\:\\git\\support\\jdbc-4.50.1.jar
    oracle.jar = D\:\\git\\support\\ojdbc8.jar

Note that this file is ephemeral and local:
it is ignored by Mercurial because it is named in ``.gitignore``.


Check the Configuration of the Build
------------------------------------

Run the ``full-check`` target, which does some simple checks on the repository:

..  code-block:: ps1con

    PS work> ant full-check
    Buildfile: D:\git\work\build.xml

    force-snapshot-if-polluted:

         [echo] Change set ba16fbc48 is not tagged v2.7.3a1 - build is a snapshot.

         [echo] jython.version            = '2.7.3a1-SNAPSHOT'

It makes an extensive dump, in which two lines like those above matter particularly.
See that ``build.xml`` has worked out the version string correctly,
and that it must be a snapshot build because you haven't tagged it.
Check that the rest of this dump looks like what you ordered
(version of Java correct?)
and that it ends with ``BUILD SUCCESSFUL``.

You could do a complete dry-run at this point.
It would create a snapshot build that identifies itself by the version string above.
If you want something other than "SNAPSHOT" as the qualifier,
define the property ``snapshot.name`` on the ``ant`` command line or in ``ant.properties``.

If you see a message along the lines "Workspace contains uncontrolled files"
then the files listed must be removed (or possibly added to version control) before continuing.
They may be test-droppings or the by-product of your last-minute changes.


Tag the Release
---------------

Ensure you have committed any outstanding changes (none in this example)
and tag the final state as the release,
being careful to observe the conventional pattern
(there *is* a "v" and there are *two* dots):

..  code-block:: ps1con

    PS work> git tag -a -s v2.7.3a1 -m"Jython 2.7.3a1"

Note that ``git tag -a`` creates a sort of commit.
It will need to be pushed eventually,
but the current state of your repository is still at the change set tagged.
If something goes wrong after this point, but before the eventual push to the repository,
requiring changes and a fresh commit,
it is possible to delete the tag with ``git tag -d v2.7.3a1``.
Do not `delete a tag after the push`_.

We follow CPython in signing the tag with GPG as indicated in :pep:`101`
and the `CPython release-tools`_.
See the section :ref:`PGP-signing` for how to generate a key.
(If you are doing a dry-run you can avoid the signing by dropping the `-s` option.)

As explained in `signing Git commits with GPG`_,
``gpg`` as supplied with *Git for Windows*
and *GnuPG for Windows* disagree about the location of your keys.
In order for signing to work,
it may be necessary to prepare your installation of Git (one time only)
to select the full version of *GnuPG for Windows* as follows.

..  code-block:: ps1con

    git config --global gpg.program $env:localappdata\gnupg\bin\gpg.exe


.. _signing Git commits with GPG: https://jamesmckay.net/2016/02/signing-git-commits-with-gpg-on-windows/
.. _CPython release-tools: https://github.com/python/release-tools
.. _delete a tag after the push: https://git-scm.com/docs/git-tag#_discussion


Ant Build for Release
---------------------

Run the ``full-check`` target again:

..  code-block:: ps1con

    PS work> ant full-check
    Buildfile: D:\git\work\build.xml

         [echo] Build is for release of 2.7.3a1.

         [echo] jython.version            = '2.7.3a1'

This time the script confirms it is a release
and the version appears without the "SNAPSHOT" qualifier.

If all remains well with the properties dumped, run the ``full-build`` target.
This outputs the same dump as ``full-check`` and goes on to build the release artifacts.
``build.xml`` does not force a snapshot build on you now
because the source tree is clean and the tag corresponds to the version.

The artifacts of interest are produced in the ``./dist`` directory and they are:

#. ``jython.jar``
#. ``jython-installer.jar``
#. ``jython-standalone.jar``
#. ``sources.jar``
#. ``javadoc.jar``

..  note:: At the time of writing, the ``javadoc`` sub-target produces many warnings.
    Java 8 is much stricter than Java 7 about correct Javadoc.
    These are not fatal to the build:
    they are a sign that our documentation is a bit shabby (and always was secretly).


Gradle Build for Release
------------------------

We can also build a slim JAR (one *not* containing its dependencies) using Gradle.
At the time of writing, the Gradle build is considered experimental.
We have little experience using this JAR for applications.
Gradle operates a build entirely parallel to the Ant build,
where everything is regenerated from source,
working in folder ``./build2``.

..  code-block:: ps1con

    PS work> .\gradlew --console=plain publish
    > Task :generateVersionInfo
    This build is for v2.7.3a1.

    > Task :generatePomFileForMainPublication
    > Task :generateGrammarSource
    > Task :compileJava
    > Task :expose
    > Task :mergeExposed
    > Task :mergePythonLib
    > Task :copyLib
    > Task :processResources
    > Task :classes
    > Task :pycompile
    > Task :jar
    > Task :javadoc
    > Task :javadocJar
    > Task :sourcesJar
    > Task :publishMainPublicationToStagingRepoRepository
    > Task :publish

    BUILD SUCCESSFUL in 6m 59s
    15 actionable tasks: 15 executed

When the build finishes, a JAR that is potentially fit to publish,
and its subsidiary artifacts (source, javadoc, checksums),
will have been created in ``./build2/stagingRepo/org/python/jython-slim/2.7.3a1``.

It can also be published to your local Maven cache (usually ``~/.m2/repository``
with the task ``publishMainPublicationToMavenLocal``.
This need not be done as part of a release,
but can be useful in verification using a Gradle or Maven build that references it
(see the section :ref:`jython-slim-regrtest`).

.. _test-what-you-built:

Test what you built
-------------------

At this point, take the stand-alone and installer JARs to an empty directory elsewhere,
and try to use them in a new shell session.
In the example, the local directory ``inst`` is chosen as the target in the installer.
Let's use Java 11, different from the version we built with.

..  code-block:: ps1con

    PS 273a1-trial> mkdir kit
    PS 273a1-trial> copy "D:\git\work\dist\jython*.jar" .\kit
    PS 273a1-trial> java -jar kit\jython-installer.jar
    WARNING: An illegal reflective access operation has occurred
    ...
    DEPRECATION: A future version of pip will drop support for Python 2.7.
    ...
    Successfully installed pip-19.1 setuptools-41.0.1

It is worth checking the manifests:

..  code-block:: ps1con

    PS 273a1-trial> jar -xf .\kit\jython-standalone.jar META-INF
    PS 273a1-trial> cat .\META-INF\MANIFEST.MF
    Manifest-Version: 1.0
    Ant-Version: Apache Ant 1.9.14
    Created-By: 1.8.0_241-b07 (Oracle Corporation)
    Main-Class: org.python.util.jython
    Built-By: Jeff
    Implementation-Vendor: Python Software Foundation
    Implementation-Title: Jython fat jar with stdlib
    Implementation-Version: 2.7.3a1

    Name: Build-Info
    version: 2.7.3a1
    git-build: true
    oracle: true
    informix: true
    build-compiler: modern
    jdk-target-version: 1.8
    debug: true

And similarly in other JARs ``inst\jython.jar``, ``kit\jython-installer.jar``.


Installation ``regrtest``
^^^^^^^^^^^^^^^^^^^^^^^^^

The real test consists in running the regression tests:

..  code-block:: ps1con

    PS 273a1-trial> inst\bin\jython -m test.regrtest -e
    == 2.7.3a1 (tags/v2.7.3a1:625fdf3b1, Jun 2 2020, 20:06:45)
    == [Java HotSpot(TM) 64-Bit Server VM (Oracle Corporation)]
    == platform: java11.0.6
    == encodings: stdin=ms936, stdout=ms936, FS=utf-8
    == locale: default=('en_GB', 'GBK'), actual=(None, None)
    test_grammar
    test_opcodes
    test_dict
    ...
    4 fails unexpected:
        test___all__ test_java_visibility test_jy_internals test_ssl_jy

These failures are false alarms.

* ``test___all__``, ``test_java_visibility`` and ``test_jy_internals`` fail
  because we (deliberately) do not include certain JARs.
* ``test_sort`` fails intermittently on later versions of Java.
* ``test_ssl_jy`` fails because of `bjo issue 2858`_.

.. _bjo issue 2858: https://bugs.jython.org/issue2858


Stand-alone ``regrtest``
^^^^^^^^^^^^^^^^^^^^^^^^

The stand-alone JAR does not include the tests,
but one may run them by supplying a copy of the test modules as below.
The point of copying (only) the test directory to ``TestLib/test``,
rather than putting ``inst/Lib`` on the path,
is to ensure that other modules are tested from the stand-alone JAR itself.
There will be many failures (34 when the author last tried).

..  code-block:: ps1con

    PS 273a1-trial> copy -r inst\Lib\test TestLib\test
    PS 273a1-trial> $env:JYTHONPATH = ".\TestLib"
    PS 273a1-trial> java -jar .\kit\jython-standalone.jar -m test.regrtest -e
    == 2.7.3a1 (tags/v2.7.3a1:625fdf3b1, Jun 2 2020, 20:06:45)
    == [Java HotSpot(TM) 64-Bit Server VM (Oracle Corporation)]
    == platform: java11.0.6
    == encodings: stdin=ms936, stdout=ms936, FS=utf-8
    == locale: default=('en_GB', 'GBK'), actual=(None, None)    test_grammar
    test_opcodes
    ...
    34 fails unexpected:
        test_argparse test_classpathimporter test_cmd_line
        test_cmd_line_script test_codecs_jy test_compile_jy test_email_jy
        test_email_renamed test_httpservers test_import test_import_jy
        test_inspect test_java_integration test_java_visibility test_json
        test_jy_internals test_jython_initializer test_jython_launcher
        test_lib2to3 test_linecache test_marshal test_os_jy test_pdb
        test_platform test_popen test_quopri test_repr test_site
        test_site_jy test_ssl_jy test_sys test_threading test_warnings
        test_zipimport_support

Most of these failures are in tests that assume the library is a real file system.
Others arise because we do not include certain JARs needed for the test.
It is necessary to pick through the failures carefully to detect which are real.

.. note:: We could probably do this better through skips in the tests,
   sensitive to running stand-alone,
   or (widely useful) a broader interpretation of "file path" in Jython,
   reflecting the importance of the JAR file system in Java.

   We should do this occasionally, and not just when trying to release.
   Some of the failures are genuine problems,
   by chance revealed only in the stand-alone version.


.. _jython-slim-regrtest:

Slim (Gradle) ``regrtest``
^^^^^^^^^^^^^^^^^^^^^^^^^^

There is not currently a pre-prepared way to test the Gradle-built JAR (``jython-slim``),
but it is not difficult to create something.
For this, it is necessary to publish to a local repository, such as your personal Maven cache:

..  code-block:: ps1con

    PS work> .\gradlew --console=plain publishMainPublicationToMavenLocal

This will deliver build artifacts to ``~/.m2/repository/org/python/jython-slim/2.7.3a1``.
One can construct an application to run with that as a dependency like this:

..  code-block:: groovy

    // build.gradle for applications importing the jython-slim JAR.
    plugins {
        id 'java'
    }
    sourceCompatibility = '1.8'
    targetCompatibility = '1.8'
    version = '0.0.1'

    repositories {
        mavenLocal()
        mavenCentral()
    }

    dependencies {
        implementation 'org.python:jython-slim:2.7.3a1'
    }

The following executes ``test.regrtest`` using the same local copy of the tests
prepared for the stand-alone Jython,
and has about the same success rate.

..  code-block:: java

    package uk.co.farowl.jython.slimdemo;
    import org.python.util.PythonInterpreter;
    public class RegressionTest {
        public static void main(String[] args) {
            PythonInterpreter interp = new PythonInterpreter();
            interp.exec("import sys, os");
            interp.exec("sys.path[0] = os.sep.join(['.', 'TestLib'])");
            interp.exec("from test import regrtest as rt");
            interp.exec("rt.main(expected=True)");
        }
    }


Only now is it safe to ``git push``
-----------------------------------

If testing convinces you this is a build we should let loose on an unsuspecting public,
it is time to push these changes and the tag you made upstream to the Jython repository.
Back in the place where the release was built (and using Bash):

..  code-block:: bash

    $ git push --follow-tags

Try very hard not to push a tag you later regret
(e.g. on the wrong change set or a version still needing a fix).
It is problematic to `delete a tag after the push`_.
It is better to increment the version,
which is painless if it is an ``a``, ``b``, or ``rc`` release.


Build the Bundles to Publish
----------------------------

The artifacts for Maven are built using a separate script ``maven/build.xml``.

..  code-block:: text

    PS work> ant -f maven\build.xml
    Buildfile: D:\git\work\maven\build.xml
    ...
    BUILD SUCCESSFUL
    Total time: 24 seconds
    PS work>

During the build, ``gpg`` may prompt you (in a dialogue box)
for the pass-phrase that protects your private signing key.
This leaves the following new artifacts in ``./publications``:

* ``jython-2.7.3a1-bundle.jar``
* ``jython-standalone-2.7.3a1-bundle.jar``
* ``jython-installer-2.7.3a1-bundle.jar``
* ``jython-slim-2.7.3a1-bundle.jar``


Publication
===========

Account
-------

In order to publish the bundles created in ``./publications``,
it is necessary to have an account with access to ``groupId`` ``org.python``,
which Sonatype will grant given the support of an existing owner.
(This is a human process administered through JIRA.)
There is an extensive `Sonatype OSSRH Guide`_ about getting and using an account.
These notes indicate a particular path that worked for the author.

.. _PGP-signing:

PGP Signing
-----------

You need a PGP signing key pair (generated with ``gpg --gen-key``)
on the computer where you are working.
And this must be published through the pool of PGP key servers for Sonatype to pick up,
as for example here:

..  code-block:: text

    PS work> gpg --list-secret-keys
    ...
    pub   rsa2048 2019-10-20 [SC] [expires: 2021-10-19]
          C8C4B9DC1E031F788B12882B875C3EF9DC4638E3
    uid           [ultimate] Jeff Allen <ja.py@farowl.co.uk>
    sub   rsa2048 2019-10-20 [E] [expires: 2021-10-19]

    PS work> gpg -v -a --export C8C4B9DC1E031F788B12882B875C3EF9DC4638E3
    gpg: writing to stdout
    -----BEGIN PGP PUBLIC KEY BLOCK-----

    mQENBF2sVzEBCACqCLAn02agm66fsdgfEEDhPGlJv/BhX/GDsw/FwCzpwZLyiFiJ
    vwcMWgV0OOP3gI2CkzPxFE9/LL3Zjc/vS9J0tGeDBsCPB7TGiToQm63+zyzNCU3R
    ...
    6lHuw4ISj/zWIkIcDf7n1JYKitc0L6oVtgnPDFcXKfAhOTOqR8ho
    =Snxq
    -----END PGP PUBLIC KEY BLOCK-----

This text could instead have been directed to a file with the option ``-o filename``.

The `OpenPGP key server`_ provides an interface to query or submit a PGP public key.
Visit there with a browser and choose "Submit Key".
Paste the text exported into the dialogue,
including the ``BEGIN`` and ``END`` markers.
Once the key is published (takes a few minutes),
the query interface can find the newly-published key from the query ``0xNNNN``,
where ``NNNN`` is the last 16 hex digits of the UID (``875C3EF9DC4638E3`` here).

PGP servers form a pool.
It may take a few hours for your key to wash up at the machine Sonatype consults.

..  note:: The method recommended in the Sonatype documentation is:

    ..  code-block:: text

        PS work> gpg --keyserver hkp://pool.sks-keyservers.net --send-keys C8C4...38E3

    This did not result (for the author) in the key being published,
    even half a day later.
    This may be a local problem that the web interface circumvents.
    It may have been a transient problem with ``sks-keyservers.net``.

Generation and publication of a key are one-time actions,
except that the key has a finite lifetime (2 years here).

.. _Sonatype OSSRH Guide: https://central.sonatype.org/pages/ossrh-guide.html
.. _OpenPGP key server: http://keys.gnupg.net


Publication via Sonatype
------------------------

You are now ready to upload bundles acceptable to Sonatype.

* Go to the Sonatype_ repository manager and log in.
* Under "Build Promotion" select "Staging Upload".
* On the "Staging Upload" tab, and the Upload Mode drop-down, select "Artifact Bundle".
* Navigate to the ``./publications`` folder and upload in turn:

  * ``jython-2.7.3a1-bundle.jar``
  * ``jython-standalone-2.7.3a1-bundle.jar``
  * ``jython-installer-2.7.3a1-bundle.jar``
  * ``jython-slim-2.7.3a1-bundle.jar``

  For some reason the display shows a fake file path but the name is correct.
  Each upload creates a "staging repository".

.. Staging URL has form: https://oss.sonatype.org/content/repositories/orgpython-1073

* Under "Build Promotion" select the "Staging Repositories" tab.
* Check (on the "Activity" tab) that the upload reached "Close" with good status,
  If not, it should tell you what is lacking and you have to go back and fix it.
* In a fresh directory,
  download the (as yet private) artifacts from Sonatype and test them,
  repeating the section :ref:`test-what-you-built`.
* When you are absolutely satisfied, ... "Release" the bundles.
  This will cause them to appear in the Maven `Central Repository`_ (takes an hour or two).

.. warning:: Release at Sonatype is irreversible.

.. _Central Repository: https://search.maven.org/


Announcement
------------

.. note:: This section is slightly modified from Frank's notes, untested since recent changes.

* update files in (or make a PR against) the `website repository`_
  that reference the current release:

  * Add to the `website news page`_ (``news.md``)
  * Ensure links on the `website front page`_ (``index.md``)
    and `website download page`_ (``download.md``) reflect:

    * the latest stable release
    * the current alpha, beta, or candidate release (if any to be advertised)

  Exactly what you do here will depend on the kind of release you just made.

* change the ``#jython`` irc channel topic
* announce on twitter (as jython), irc channel, mailing lists, blog ...
* In the bug tracker:

  * add the new version, against which to report bugs.
  * add a new milestone (future version), against which to plan delivery.

.. _website repository: https://github.com/jython/jython.github.io
.. _website front page: https://www.jython.org/index
.. _website news page: https://www.jython.org/news
.. _website download page: https://www.jython.org/download



Ready for new work
==================

After a release,
Jython in the development environment
should no longer identify itself as the version just released, so we increment the version string.
We do not know for sure the version next to be publicly released,
so we use the smallest increment that results in a valid version number.

After an alpha, beta or release candidate,
assume the successor version to be a one-up serial of the same release level,
incrementing ``jython.release_serial``.
After a final release, assume the successor to be an alpha of the next micro-release.
For example, ``2.7.2b2`` is followed by ``2.7.2b3``, and ``2.7.2`` by ``2.7.3a1``.

The build system will label the code as ``2.7.3a2-DEV`` in the developer build.
If you build an installer, or dry-run a release, it will be ``2.7.3a2-SNAPSHOT``.
You can read this as a version that "may eventually become" ``2.7.3a2`` etc..

Make this change in both ``build.xml`` and ``build.gradle``.
See the section :ref:`changes-preparing-for-a-release` for details.

In ``NEWS``, add a new, empty, section in the development history that looks like this:

..  code-block:: text

    Jython <successor version>
      Bugs fixed

      New Features

Commit and push this change upstream.

.. note:: Since new features should not be added after ``b1``, this structure doesn't quite work.
   It places "New Features" a long way from the top of NEWS.
