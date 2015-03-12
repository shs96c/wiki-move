# Introduction

The steps to releasing Se Grid from GitHub.  _This will need to be updated when the code moves into SVN._


# Details

  1. Make sure tests are passing on master
  1. Update the Changelog
  1. Update the project.properties to use the new version number
  1. Update hub/src/main/java/com/thoughtworks/selenium/grid/hub/management/console/index.html to use the new version number
  1. Tag the release in git
  1. Push the tag up to GitHub
  1. Build the distribution: ant dist
  1. Upload the distribution to /tmp on releases.seleniumhq.org
  1. Become the release user (sudo su - release) and copy the distribution to release.openqa.org/htdocs/selenium-grid/
  1. Become your regular user account and remove the file you uploaded to /tmp
  1. Update website release page
  1. Test download
  1. Make announcement on user list