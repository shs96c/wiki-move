# FirefoxDriver Internals

The FirefoxDriver is largely written in the form of a Firefox extension. Language bindings control the driver by connecting over a socket and sending commands (described in the JsonWireProtocol page) in UTF-8. The extension makes use of the XPCOM primitives offered by Firefox in order to do its work. The important thing to notice is that the command names map directly on to methods exposed on the "FirefoxDriver.prototype" in the javascript code.

# Working on the FirefoxDriver Code

Firstly, make sure that there's no old version of the FirefoxDriver installed:

  * Start firefox's profile manager: `firefox -ProfileManager`
  * Delete the existing WebDriver profile if there is one. Delete the files too (it's an option that's offered when you delete the profile in the profile manager)

Secondly, take a look at the [Mozilla Developer Center](http://developer.mozilla.org/en/docs/Extensions), particularly the section to do with [setting up an extension development environment](http://developer.mozilla.org/en/docs/Setting_up_extension_development_environment). You should now be ready to edit code. It's best to create a test around the area of code that you're working on, and to run this using the `SingleTestSuite`. The FirefoxDriver logs errors to Firefox's error console ("Tools->Error Console"), so if a test fails, that's a great place to start looking.

To actually log information to the console, use the "`Utils.dumpn()`" method in your javascript code. If you find that you'd like to examine an object in detail, use the "`Utils.dump()`" method, which will report which interfaces an object implements, as well as outputting as much information as it can to the console..

## Flow of Control: Starting Firefox

The following steps are performed when instantiating an instance of the FirefoxDriver:

  1. Grab the "locking port"
    1. 