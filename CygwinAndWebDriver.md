# Prerequisites

To use rake, you need to have installed the Ruby package with cygwin.  If you didn't (check this by running `which ruby` - it should point at /usr/bin/ruby.  If it's something beginning with /cygdrive, you have not installed cygwin Ruby.  You will probably get errors complaining about "No medium" if you try to run non-cygwin Ruby).

Make sure your environment variable $RUBYOPT is blank (specifically, does not contain -rubygems).

You then need to install RubyGems.  Do this by:
  1. Download the RubyGems tarball from [Ruby Forge](http://rubyforge.org/projects/rubygems/)
  1. Unpack the tarball (tar xvfz rubygems-`*`.tgz)
  1. Navigate to the unpacked tarball
  1. Run the following command: `ruby setup.rb install`
  1. Update RubyGems by running the following: `gem update --system` (You may need to do this twice).

Then install rake.  Do this by: `gem install --remote rake`

`which rake` should now show /usr/bin/rake

You may need to restart cygwin at this stage.

You should now be able to build webdriver from rake.  See DeveloperTips for more information about building from rake.