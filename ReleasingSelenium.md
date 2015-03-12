

# Release criteria

The release manager needs to evaluate if the codebase is ready for release. This normally involves some subjective judgements. A few things can be noted:

  * `go test_selenium` should pass
  * chromedriver is not part of the project. While test failures are /interesting/, they are normally not blocking. Running tests without chromedriver on your path simplifies things a bit.

Known issues can be added to the release notes.

# For Everything

  1. Gather feedback from other developers about the release: Define a time for the release, ask for release blockers and provide additional details that will help developers participate in the release process. This should be emailed to  'selenium-developers' though working on IRC alone is just fine.
  1. Bump the revision numbers at the top of the Rakefile.
  1. Bump the revision number in "javascript/firefox-driver/extension/install.rdf"
  1. Bump the revision number in "java/server/src/org/openqa/selenium/server/VERSION.txt" and "javascript/selenium-core/scripts/selenium-version.js"
  1. Commit version number bump.
  1. To find out the revision of the last release: `git log --tags --simplify-by-decoration --pretty="format:%ci %d %h" | sort | tail -1 | awk '{print $NF}'`. This will give you the abbreviated hashcode for the last revision.

# For Java

  1. Update the docs. I do this on a UNIX variant (**TODO: modify for git**):
    1. `./go javadocs`
    1. `rm -rf docs/api/java/*`
    1. `cp -r build/javadoc/* docs/api/java`
    1. `git add docs/api/java`
    1. `git commit docs/api`
  1. Update the Java CHANGELOG under `java/CHANGELOG`. Do this by finding the revision number of the last release and running `git log oldRev..HEAD >> java/CHANGELOG`. You'll then need to edit the file by hand.
  1. Update the SeHQ downloads page with updated links, release numbers and dates
  1. Run `./go clean release`
  1. Upload the new versions of the ZIP files from "build/dist" to the downloads page. Alternatively, run `./go push_release` to have the artifacts uploaded for you.
  1. Upload the new Android APK from build/android/app/android-server.apk to the downloads page.
  1. Add a git tag: `git tag selenium-2.<REVISION> hash` where "hash" refers to the revision to tag. This creates a lightweight tag, so there's no need to add a log message. You'll need to push that change to the origin repo with `git push origin --tags`



## Updating the Maven Repos

  1. Prerequisite 1: Make sure Maven 2.2.1 and GnuPG are installed.
  1. Prerequisite 2: Make sure your `~/.m2/settings.xml` contains the following lines in the `<servers>` section:
```
    <server>
        <id>sonatype-nexus-snapshots</id>
        <username>_your-username-for-oss.sonatype.org_</username>
        <password>_your-password_</password>
    </server>
    <server>
        <id>sonatype-nexus-staging</id>
        <username>_your-username-for-oss.sonatype.org_</username>
        <password>_your-password_</password>
    </server>
```
  1. Prerequisite 3: Make sure your GnuPG public key is distributed to `hkp://pgp.mit.edu`
  1. Make sure the dependencies in all pom.xml files under the maven directory are correct -- if not, update them (Hint: check for changes in the third\_party directory)
  1. Perform any manual steps/release hacks documented here:
    1. To provide the correct version info in a WebDriverException you should also replace "unknown" in the maven/api/pom.xml with the Git commit number wich forked the version branch:
```
             <manifestEntries>
                 <Selenium-Version>${version}</Selenium-Version>
-                <Selenium-Revision>unknown</Selenium-Revision>
+                <Selenium-Revision>12345</Selenium-Revision>
                 <Selenium-Build-Time>${maven.build.timestamp}</Selenium-Build-Time>
             </manifestEntries>
```
  1. Update the version number in all pom.xml files under the maven directory (but do not check in those changes) (`mvn versions:set -DnewVersion=2.3.0` inside the "maven" folder)
  1. Execute:
    1. `./go release`
    1. `cd maven`
    1. `mvn -Dtest=skip -DfailIfNoTests=false install`
  1. Execute:
> > `mvn source:jar javadoc:jar -Dtest=skip -DfailIfNoTests=false -Dgpg.passphrase=_your-GnuPG-key-passphrase_ -Psign deploy`
  1. Goto http://oss.sonatype.org, log in, close the staging repository.
  1. Test the artifacts, these are the _real_ files you will be irreversibly promoting to central. You can do this easily by cloning the repo at https://github.com/krosenvold/selenium-release-test and following the instructions there. (Once the staging repository has been closed you get the url to the repository by clicking on the repository. This url can be added to your project pom to test if you want to test using a different project. Remember that you may need to delete the artifacts in your local repository to make sure you re-download the staged artifacts).
  1. Promote that staged release in nexus (or drop it).
  1. Make sure any other manual hacks you did are documented in step 5 or committed back on trunk.
  1. Once the artifacts finally hit central (please wait till they are actually there), update the maven downloads page on SeHQ. If you can't wait don't update the page, someone will notice and update it.

# For .NET

  1. Verify the build number is correct in dotnet/build.desc
  1. Verify the build number is correct in the AssemblyVersion and AssemblyFileVersion attributes in the AssemblyInfo.cs file of each of the following projects:
    * Selenium.Core
    * Selenium.WebDriverBackedSelenium
    * WebDriver
    * WebDriver.Support
  1. Run `go clean //dotnet:release`. Note that this will require you to have the [.NET documentation generation tools](http://shfb.codeplex.com) installed.
  1. Upload the selenium-dotnet-xx.zip file from build/dotnet to the downloads page.
  1. Update the SeHQ downloads page with updated links, release numbers and dates

# For Ruby

## selenium-webdriver

  1. To release a new gem you will need to be registered as a gem owner on rubygems.org.
    1. Bump the version number in rb/build.desc.
    1. Update rb/CHANGES with a summary of changes since the last release.
    1. Update docs: `rm -rf docs/api/rb && ./go //rb:docs` (you'll need the YARD gem installed)
    1. Commit the changes.
    1. `./go //rb:gem:release`
    1. Bump version number in rb/build.desc to "X.X.X.dev" and commit the change.
    1. Update the SeHQ downloads page with updated links, release numbers and dates

# For Python
# To release a new version you need to be registered as a Owner/Maintener on pypi.python.org
  1. Bump the version numbers
    1. in setup.py
    1. in py/README
    1. in py/selenium/init.py
    1. in py/selenium/webdriver/init.py
    1. in py/docs/source/index.rst

  1. check that in.
  1. `./go py_release` ~ have your credentials stored in ~/.pypirc
  1. Bump the version number to the next version with dev e.g. 2.(n+1).0 and commit changes
  1. Update the SeHQ downloads page with updated links, release numbers and dates
  1. Update the pydoc:
    1. `./go py_docs`
    1. check in docs/api/py


# Areas to Improve

  * We should really automate the version number bumps.