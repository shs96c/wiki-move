Developed in collaboration with the Chromium team, [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/) is a standalone server which implements WebDriver's  [wire protocol](JsonWireProtocol.md).

## [Visit the full ChromeDriver site](https://sites.google.com/a/chromium.org/chromedriver/)

## [View all ChromeDriver downloads](https://sites.google.com/a/chromium.org/chromedriver/downloads)

The ChromeDriver consists of three separate pieces. There is the browser itself ("chrome"), the language bindings provided by the Selenium project ("the driver") and an executable downloaded from the Chromium project which acts as a bridge between "chrome" and the "driver". This executable is called "chromedriver", but we'll try and refer to it as the "server" in this page to reduce confusion.

## Requirements

The server expects you to have Chrome installed in the default location for each system:
| **OS** | **Expected Location of Chrome** |
|:-------|:--------------------------------|
| Linux  | /usr/bin/google-chrome<sup>1</sup> |
| Mac    | /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome |
| Windows XP | %HOMEPATH%\Local Settings\Application Data\Google\Chrome\Application\chrome.exe |
| Windows Vista | C:\Users\%USERNAME%\AppData\Local\Google\Chrome\Application\chrome.exe |

<sup>1</sup> For Linux systems, the ChromeDriver expects /usr/bin/google-chrome to be a symlink to the actual Chrome binary. See also the section on [overriding the Chrome binary location](ChromeDriver#Overriding_the_Chrome_binary_location.md) .

## Getting Started

**Read [ChromeDriver user documentation](https://sites.google.com/a/chromium.org/chromedriver/home)**

### Running ChromeDriver as a standalone process

Since the ChromeDriver implements the wire protocol, it is fully compatible with any RemoteWebDriver client. Simply start up the ChromeDriver executable (that works as a server), create a client, and away you go:
```
WebDriver driver = new RemoteWebDriver("http://localhost:9515", DesiredCapabilities.chrome());
driver.get("http://www.google.com");
```

### Troubleshooting

If you are using the RemoteWebDriver and you get the _The path to the chromedriver executable must be set by the webdriver.chrome.driver system property_ error message you likely need to check that one of these conditions is met:

  * The chromedriver binary is in the system path, or
  * The Selenium Server was started with -Dwebdriver.chrome.driver=c:\path\to\your\chromedriver.exe

[ChromeDriver user documentation](https://sites.google.com/a/chromium.org/chromedriver/home) provides more information on the known issues and workarounds.

### Think you've found a bug?

Check if the bug has been [reported](http://code.google.com/p/chromedriver/issues/list) yet.  If it hasn't, please open a [new issue](http://code.google.com/p/chromedriver/issues/entry) and be sure to include the [following](SeleniumHelp.md):

  * What platform are you running on?
  * What version of the chromedriver are you using?
  * What version of Chrome are you using?
  * The failure stacktrace, if available.
  * The contents of chromedriver's log file (chromedriver.log).

Of course, if your bug has already been reported, you can update the issue with the information above.  Having more information to work on makes it easier for us to track down the cause of the bug.

## Testing earlier versions of Chrome

ChromeDriver is only compatible with Chrome version 12.0.712.0 or newer. If you need to test an older version of Chrome, use Selenium RC  and a Selenium-backed WebDriver instance:
```
URL seleniumServerUrl = new URL("http://localhost:4444");
URL serverUnderTest = new URL("http://www.google.com");
CommandExecutor executor = new SeleneseCommandExecutor(seleniumServerUrl, serverUnderTest, DesiredCapabilities.chrome());
WebDriver driver = new RemoteWebDriver(executor);
```

## More ChromeDriver links

  * [ChromeDriver capabilities](https://sites.google.com/a/chromium.org/chromedriver/capabilities)
  * [Contribution guide](https://sites.google.com/a/chromium.org/chromedriver/contributing)
  * [Getting started with ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/getting-started)
  * [Logging](https://sites.google.com/a/chromium.org/chromedriver/logging)
  * [Mobile emulation](https://sites.google.com/a/chromium.org/chromedriver/mobile-emulation)
  * [Chromedriver support policy](https://sites.google.com/a/chromium.org/chromedriver/support-policy)
  * [Need help?](https://sites.google.com/a/chromium.org/chromedriver/help)