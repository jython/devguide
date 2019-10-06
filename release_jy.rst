=====================
How To Release Jython
=====================

These are the steps needed to make a public release of Jython.
In the case of a public release, of course,
you can only do this once the hard work of development and debugging is done,
and there is a consensus in the project that adequate quality has been achieved.

It will also be a useful guide if you intend to make a snapshot (private) release.

It may be tested, without making a real release, as a check that it works:
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
* Access to the channels where we announce releases (e.g. Twitter).
* Access to modify the bug-tracker configuration.

You can dry-run this process with only the first pre-requisite (driver JARs),
and a Mercurial clone of the official repository.
In that case, be careful not to push any changes.
(Hint: if you clone from ``https://hg.python.org/jython``,
that will prevent an unintended push.)

.. _Sonatype: https://oss.sonatype.org


Making a Releasable Jython
==========================

Start in the Right Place
------------------------

``cd`` to a directory where you have permission to make sub-directories.
The path to this directory can get embedded in published files,
so aim for:

* short (i.e. near a file system root).
* impersonal (not containing company or personal names).
* ASCII (even though Jython is pretty good with Unicode now).

The examples in this text were made with Windows PowerShell session at ``D:\hg``.

We must build with the right version of Java.
(At the time of writing we target Java 7.)
At the same time, let's check that we have the tools we need on the path:

.. code-block:: ps1con

    PS hg> hg version -q
    Mercurial Distributed SCM (version 3.9.2)
    PS hg> ant -version
    Apache Ant(TM) version 1.9.7 compiled on April 9 2016
    PS hg> java -version
    java version "1.7.0_80"


Clone the Repository
--------------------


Clone the repository to a named sub-directory and ``cd`` into it:

.. The quotes around arguments in these console sessions are mostly to circumvent
   shortcomings in the pygments ps1con parser.

..  code-block:: ps1con

    PS hg> hg clone "ssh://hg@hg.python.org/jython" work
    4207 files updated, 0 files merged, 0 files removed, 0 files unresolved
    PS hg> cd work
    PS work> hg id -b
    default


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
  version 2.7.2b1 is spelled ``2``, ``7``, ``2``, ``${PY_RELEASE_LEVEL_BETA}``, ``1``.
  Every other expression needing a version number is derived from these 5 values.
* ``build.gradle``: The version number appears as a simple string property ``version``,
  near the top of the file.
  Version 2.7.2b1 is simply set like this: ``version = '2.7.2b1'``.
* ``src/org/python/core/imp.java``: If there has been any compiler change,
  increment the magicnumber ``APIVersion``.
  This magic declares old compiled files incompatible, forcing a fresh compilation.
  (Maybe do it anyway, if it's been a long time.)
* ``README.txt``: It is possible no change is needed at all,
  and if a change is needed, it will probably only be to the running text.
  A copy of this file is made during the build,
  in which information from ``build.xml`` replaces the place-holders.
  (The place-holders look like ``@jython.version@``, etc..)
  This is the text a user sees when installing interactively.
  It automatically includes a prominent banner when making a snapshot build.
* ``NEWS``: First try to ensure we have listed all issues closed since the last release.
  The top of this file looks like:

  ..  code-block:: text

      Development tip
        Bugs fixed
          - [ NNNN ] ...

  Replace "Development tip" with the release you are building e,g, "Jython 2.7.2b1".
  Add anything necessary to the section "New Features".
  After publication (not now),
  we will add a new, empty, section for the "Development tip".

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

Commit this set of changes locally:

..  code-block:: ps1con

    PS work> hg commit -m"Prepare for 2.7.2b1 release."


Get the JARs
------------

Find the database driver JARs from reputable sources.

The Informix driver may be obtained from Maven Central.
Version ``jdbc-4.50.1.jar`` is known to work on Java 8.

The Oracle JDBC driver may be found at ``download.oracle.com``.
An account is required, the same one you use to update your JDK.
(The JARs on Maven Central seem to be unofficial postings.)
For Java 8 use ``ojdbc8.jar``.

Let's assume we put the JARs in ``D:\hg\support``.
Create an ``ant.properties`` correspondingly:

..  code-block:: properties

    # Ant properties defined externally to the release build.
    informix.jar = D\:\\hg\\support\\jdbc-4.50.1.jar
    oracle.jar = D\:\\hg\\support\\ojdbc8.jar

Note that this file is ephemeral and local:
it is ignored by Mercurial because it is named in ``.hgignore``.


Check the Configuration of the Build
------------------------------------

Run the ``full-check`` target, which does some simple checks on the repository:

..  code-block:: ps1con

    PS work> ant full-check
    Buildfile: D:\hg\work\build.xml

    force-snapshot-if-polluted:

         [echo] Change set b9a86440deb3 is not tagged v2.7.2b1 - build is a snapshot.

         [echo] jython.version            = '2.7.2b1-SNAPSHOT'

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

    PS work> hg tag v2.7.2b1

Note that ``hg tag`` creates a commit, on top of the one tagged,
that contains the change to ``.hgtags`` to define the tag.
This means that the current state of your repository is one commit beyond the one tagged.


Ant Build for Release
---------------------

Update to the change set you tagged, and run the ``full-check`` target again:

..  code-block:: ps1con

    PS work> hg update v2.7.2b1
    1 files updated, 0 files merged, 0 files removed, 0 files unresolved

    PS work> ant full-check
    Buildfile: D:\hg\work\build.xml

         [echo] Build is for release of 2.7.2b1.

         [echo] jython.version            = '2.7.2b1'

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
    they are a sign that our documentation is (and lways was) a bit shabby..

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
    This build is for v2.7.2b1.

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
will have been created in ``./build2/stagingRepo/org/python/jython-slim/2.7.2b1``.

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

    PS 272b-trial> mkdir kit
    PS 272b-trial> copy "D:\hg\work\dist\jython*.jar" .\kit
    PS 272b-trial> java -jar kit\jython-installer.jar
    WARNING: An illegal reflective access operation has occurred
    ...
    DEPRECATION: A future version of pip will drop support for Python 2.7.
    ...
    Successfully installed pip-19.1 setuptools-41.0.1

It is worth checking the manifests:

..  code-block:: ps1con

    PS 272b-trial> jar -xf .\kit\jython-standalone.jar META-INF
    PS 272b-trial> cat .\META-INF\MANIFEST.MF
    Manifest-Version: 1.0
    Ant-Version: Apache Ant 1.9.7
    Created-By: 1.8.0_211-b12 (Oracle Corporation)
    Main-Class: org.python.util.jython
    Built-By: Jeff
    Implementation-Vendor: Python Software Foundation
    Implementation-Title: Jython fat jar with stdlib
    Implementation-Version: 2.7.2b1
    
    Name: Build-Info
    version: 2.7.2b1
    hg-build: true
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

    PS 272b-trial> inst\bin\jython -m test.regrtest -e
    == 2.7.2b1 (v2.7.2b1:328e162ec117, Oct 6 2019, 06:46:46)
    == [Java HotSpot(TM) 64-Bit Server VM (Oracle Corporation)]
    == platform: java11.0.3
    == encodings: stdin=ms936, stdout=ms936, FS=utf-8
    == locale: default=('en_GB', 'GBK'), actual=(None, None)
    test_grammar
    test_opcodes
    test_dict
    ...
    4 fails unexpected:
        test___all__ test_java_visibility test_jy_internals test_ssl_jy

These failures are false alarms.

* ``test_java_visibility`` and ``test_jy_internals`` fail
  because we (deliberately) do not include certain JARs.
* ``test_sort`` fails intermittently on later versions of Java.
* ``test_ssl_jy`` fails because of our shading of ``bouncycastle`` classes.


Stand-alone ``regrtest``
^^^^^^^^^^^^^^^^^^^^^^^^

The stand-alone JAR does not include the tests,
but one may run them by supplying a copy of the test modules as below.
The point of copying (only) the test directory to ``TestLib/test``,
rather than putting ``inst/Lib`` on the path,
is to ensure that other modules are tested from the stand-alone JAR itself.
There will be many failures (34 when the author last tried).

..  code-block:: ps1con

    PS 272b-trial> copy -r inst\Lib\test TestLib\test
    PS 272b-trial> $env:JYTHONPATH = ".\TestLib"
    PS 272b-trial> java -jar .\kit\jython-standalone.jar -m test.regrtest -e
    == 2.7.2b1 (v2.7.2b1:328e162ec117, Oct 6 2019, 06:46:46)
    == [Java HotSpot(TM) 64-Bit Server VM (Oracle Corporation)]
    == platform: java11.0.3
    == encodings: stdin=ms936, stdout=ms936, FS=utf-8
    == locale: default=('en_GB', 'GBK'), actual=(None, None)
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
(We should do this occasionally, and not just when trying to release.
Some of the failures shon in the example are genuine problems,
by chance revealed only in the stand-alone version.)

.. note:: We could probably do this better through skips in the tests,
   sensitive to running stand-alone,
   or (widely useful) a broader interpretation of "file path" in Jython,
   reflecting the importance of the JAR file system in Java.



.. _jython-slim-regrtest:

Slim (Gradle) ``regrtest``
^^^^^^^^^^^^^^^^^^^^^^^^^^

There is not currently a pre-prepared way to test the Gradle-built JAR (``jython-slim``),
but it is not difficult to create something.
For this, it is necessary to publish to a local repository, such as your personal Maven cache:

..  code-block:: ps1con

    PS work> .\gradlew --console=plain publishMainPublicationToMavenLocal

This will deliver build artifacts to ``~/.m2/repository/org/python/jython-slim/2.7.2b1``.
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
        implementation 'org.python:jython-slim:2.7.2b1'
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


Only now is it safe to ``hg push``
----------------------------------

If testing convinces you this is a build we should let loose on an unsuspecting public,
it is time to push these changes and the tag you made upstream to the Jython repository.
Back in the place where the release was built:

..  code-block:: ps1con

    PS work> hg push

It *is* possible to recover from tagging the wrong change set,
even after a push.
One may force in a duplicate tag (``hg tag -f v2.7.2b1``),
and the later one seems to win in common tools,
but both will be present in ``.hgtags``.
It is better to avoid downstream confusion by not pushing forced tags.


Build the Bundles to Publish
----------------------------

The artifacts for Maven are built using a separate script ``maven/build.xml``.

..  code-block:: text

    PS work> ant -f maven\build.xml
    Buildfile: D:\hg\work\maven\build.xml
    ...
    BUILD SUCCESSFUL
    Total time: 24 seconds
    PS work>

This leaves the following new artifacts in ``~/publications``:

* ``jython-installer-2.7.2b1-bundle.jar``
* ``jython-2.7.2b1-bundle.jar``
* ``jython-slim-2.7.2b1-bundle.jar``
* ``jython-standalone-2.7.2b1-bundle.jar``


Publication
===========

Publication via Sonatype
------------------------

.. note:: This section is slightly modified from Frank's notes, untested recently.

* go to Sonatype_
* select "Artifact Bundle" for "Upload Mode".
* Upload the following from ``~/publications``:

  * ``jython-installer-2.7.2b1-bundle.jar``
  * ``jython-2.7.2b1-bundle.jar``
  * ``jython-slim-2.7.2b1-bundle.jar``
  * ``jython-standalone-2.7.2b1-bundle.jar``

..  note:: We should probably add: In a fresh directory,
    download the (as yet private) artifacts from Sonatype and test them,
    repeating the section :ref:`test-what-you-built`.
    When you are absolutely satisfied, ...

* "Release" the bundles when they are known to work.

.. warning:: Release at Sonatype is irreversible.


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

In ``NEWS``, add a new, empty, section in the development history that looks like this:

..  code-block:: text

    Development tip
      Bugs fixed

      New Features

Commit and push this change upstream.

There is nothing to change in ``build.xml`` after publication:
the Jython from a regular developer build will identify itself as (for example) ``2.7.2b1+``,
signifying "somewhere beyond" the version just published.
and a full build or installer will appear as ``2.7.2b1-SNAPSHOT``,
if not built from the tag as above.

.. note:: It may be better *always* to move the version on at this point,
   and have the build produce "<version>~DEV" (or something)
   meaning "somewhere short of" the version we are working towards.

