=====================
How To Release Jython
=====================

These are just some rough notes on the steps needed to make a full release of
Jython. I generally run ant full-build as a test beforehand, as well as testing
many of these steps throughout, but since that isn't strictly necessary I'm not
including it here. full-build requires all of the optional jars for the build
be available and named in ant.properties. See build.xml for more information.

* Update files in trunk that have information on the current version
    * build.xml - update properties jython.version, version.noplus, jython.major_version, jython.minor_version, jython.micro_version, jython.release_level, and jython.release_serial.
    * imp.java - If there has been any compiler change, increment magic number APIVersion.
    * NEWS (double check with the bug tracker)
    * README

* Run regrtest and the bugtests
* tag the release: hg tag v2.5.3rc1
* build from tag
* "hg pull;hg up" to get the revision number incremented by the tagging above.
* set local properties in ant.properties, mine for 2.5.3rc1:
    * informix.jar=${basedir}/extlibs/ifxjdbc.jar
    * oracle.jar=${basedir}/extlibs/ojdbc14.jar
    * jython.version=2.5.3rc1
    * jython.version.noplus=2.5.3rc1
    * project.version=2.5.3-rc1

* ant -f maven/build.xml
* go to https://oss.sonatype.org/index.html#welcome
* select "Artifact Bundle" for "Upload Mode".
* Upload jython-installer-2.5.4-rc1-bundle.jar
* Repeat for jython-2.5.4-rc1-bundle.jar
* Repeat for jython-standalone-2.5.4-rc1-bundle.jar
* Test the artifacts from sonatype.
* "Release" bundles when they are known to work (warning: this is irreversible)

Post Publish
============

* update files in the website that reference the current release
    * index.txt - news and link to the new download
    * redirects/downloads.txt - link to the new download, checksums (from file properties in the SF file manager)
    * redirects/latest.txt - a copy of NEWS
    * redirect/constants.txt - if there is a new stable release
    * building and uploading of the website is described in README.txt

* change the #jython irc channel topic
* announce on twitter (as jython), irc channel, mailing lists, blog ...
* add a new level in the bug tracker
* update build.xml for trunk again

