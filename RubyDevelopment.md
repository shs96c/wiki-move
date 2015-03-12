# Introduction

This page details how to build and test the Ruby code in Selenium.

# Details

The CrazyFunBuild runs on a bundled JRuby jar and is based on Rake. Use the 'go' Rake wrapper to run the targets. Unfortunately, rvm sets GEM\_HOME and causes trouble for our jruby-complete.jar. If you use rvm, you should disable it (`rvm use system`) before using the go script.

Since this is just a wrapper for Rake, familiar commands (like `go -T` to list targets) all work.

## Setting up

Tests will be run with your local Ruby installation, which should be a version >= 1.9.2.
Make sure you have bundler and our dependencies installed by running

  1. `gem install bundler`
  1. `./go //rb:bundle`

## Building

After making changes, you need to build the code (this is needed since we depend on other parts of the project):

  * `./go //rb:firefox`
  * `./go //rb:chrome`
  * etc.

Build results go in the `build/rb` directory. You can play with your changes in a REPL from there:

> `cd build/rb && bundle console`

Using/requiring the ruby code from `rb/lib` directly is not recommended.

## Testing

| `./go //rb:unit-test` | Run unit tests for WebDriver. |
|:----------------------|:------------------------------|
| `./go //rb:firefox-test` | Run integration tests for Firefox - replace "firefox" with any driver. |
| `./go //rb:rc-client-unit-test` | Run unit tests for selenium-client (Se 1.x/RC) |
| `./go //rb:rc-client-integration-test` | Run integration tests for selenium-client (Se 1.x/RC) |

Try `./go -T | grep //rb` to see all the Ruby targets.

## Building

You can build the gem with `./go //rb:gem:build`, which will place the gem in `build/`. See ReleasingSelenium for how to make a gem release.

## Contributing

  1. Make your feature addition or bug fix.
  1. Add tests for it. This is important so we don't break it in a future version unintentionally.
  1. Create a patch: `git diff > my-feature.patch`
  1. Create a [new issue](http://code.google.com/p/selenium/issues/entry), attach the patch and add the Lang-Ruby label.