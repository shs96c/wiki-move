# Firefox Driver

Firefox driver is included in the selenium-server-stanalone.jar available in the downloads. The driver comes in the form of an xpi (firefox extension) which is added to the firefox profile when you start a new instance of FirefoxDriver.

## Pros

  * Runs in a real browser and supports Javascript
  * Faster than the InternetExplorerDriver

## Cons

  * Slower than the HtmlUnitDriver


## Important System Properties

The following system properties (read using `System.getProperty()` and set using `System.setProperty()` in Java code or the "`-DpropertyName=value`" command line flag) are used by the FirefoxDriver:

| **Property** | **What it means** |
|:-------------|:------------------|
| webdriver.accept.untrusted.certs | ?                 |
| webdriver.assume.untrusted.issuer | ?                 |
| webdriver.development | **Never use in production** Indicates that we're in development mode. |
| webdriver.enable.native.events | ?                 |
| webdriver.firefox.bin | The location of the binary used to control firefox. |
| webdriver.firefox.port | ?                 |
| webdriver.firefox.profile | The name of the profile to use when starting firefox. This defaults to webdriver creating an anonymous profile |
| webdriver.firefox.useExisting | **Never use in production** Use a running instance of firefox if one is present |
| webdriver.log.file | Log file to dump javascript console logging to |
| webdriver.firefox.logfile | Log file to dump firefox stdout/stderr to |
| webdriver.reap\_profile | Should be "true" if temporary files and profiles should not be deleted |

Normally the Firefox binary is assumed to be in the default location for your particular operating system:

| **OS** | **Expected Location of Firefox** |
|:-------|:---------------------------------|
| Linux  | firefox (found using "which")    |
| Mac    | /Applications/Firefox.app/Contents/MacOS/firefox-bin |
| Windows | %PROGRAMFILES%\Mozilla Firefox\firefox.exe |

By default, the Firefox driver creates an anonymous profile

## Running with firebug

Download the firebug xpi file from mozilla and start the profile as follows:
```
   File file = new File("firebug-1.8.1.xpi");
   FirefoxProfile firefoxProfile = new FirefoxProfile();
   firefoxProfile.addExtension(file);
   firefoxProfile.setPreference("extensions.firebug.currentVersion", "1.8.1"); // Avoid startup screen

   WebDriver driver = new FirefoxDriver(firefoxProfile);
```

### -Beta- load fast preference

There is beta feature to make firefox not wait for the full page to load after calling .get or .click. This may cause immediate find's to break, so please be sure to use an implicit or explicit wait too. This is only available for Firefox and not other browsers.
```
   FirefoxProfile fp = new FirefoxProfile();
   fp.setPreference("webdriver.load.strategy", "unstable"); // As of 2.19. from 2.9 - 2.18 use 'fast'
   WebDriver driver = new FirefoxDriver(fp);
```