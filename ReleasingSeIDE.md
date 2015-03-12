# Introduction

The steps to releasing Se-IDE (because I keep forgetting something)


# Details

  1. Download release candidate build [from Jenkins](http://ci.seleniumhq.org:8080/job/IDE/lastSuccessfulBuild/artifact/)
  1. Upload XPIs to release.seleniumhq.org
    1. SFTP to www.openqa.org and upload all XPIs from the Jenkins build used for the release
    1. SSH to www.openqa.org
    1. Copy all XPIs to /tmp
    1. Switch to the release user (sudo su - release)
    1. Copy the XPIs to the relevant locations in release.openqa.org/htdocs/selenium-ide
    1. Make sure the multi-xpi is in selenium-ide/x.x.x and the editor is in selenium-ide/editor/x.x.x
  1. Update download page on seleniumhq.org with new release details
  1. Test download
  1. Add the release to the update.rdf files the IDE iteself and all bundled plugins
    1. Add a new `<RDF:li>` at the top for the new release
    1. Make sure the following are correct
      * `<em:version>` matches the new version number
      * `<em:maxVersion>` matches the Firefox version support
      * `<em:updateLink>` matches the download link for XPI
      * `<em:updateHash>` is the correct hash for the XPI
        * You can generate this using: openssl md5 -sha1 `<file>`
        * Note that for the IDE the hash is of the multi-xpi
        * Make sure the value starts with sha1:
  1. Test upgrade
  1. Update [release notes](http://code.google.com/p/selenium/wiki/SeIDEReleaseNotes)
  1. Announce