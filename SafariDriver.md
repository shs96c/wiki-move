# The SafariDriver



The SafariDriver is implemented as a Safari browser extension.  The driver inverts the traditional client/server relationship and communicates with the WebDriver client using [WebSockets](http://en.wikipedia.org/wiki/WebSocket).

The SafariDriver requires Safari 5.1+

## Getting Started

Starting with Selenium 2.45.0, you must manually install the SafariDriver browser extension. Simply open the latest copy of [SafariDriver.safariextz](http://selenium-release.storage.googleapis.com/index.html) in Safari and click the "install" button. Once installed, writing a test for Safari is just as straightforward as using the FirefoxDriver:

```
import static org.junit.Assume.assumeTrue;

import org.openqa.selenium.By;
import org.openqa.selenium.Platform;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.safari.SafariDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.JUnit4;

@RunWith(JUnit4.class)
public class SafariDriverTests {

  private WebDriver driver = null;

  private static boolean isSupportedPlatform() {
    Platform current = Platform.getCurrent();
    return Platform.MAC.is(current) || Platform.WINDOWS.is(current);
  }

  @Before
  public void createDriver() {
    assumeTrue(isSupportedPlatform());
    driver = new SafariDriver();
  }

  @After
  public void quitDriver() {
    driver.quit();
  }

  @Test
  public void shouldBeAbleToPerformAGoogleSearch() {
    driver.get("http://www.google.com");
    driver.findElement(By.name("q")).sendKeys("webdriver");
    driver.findElement(By.name("btnG")).click();
    new WebDriverWait(driver, 3)
        .until(ExpectedConditions.titleIs("webdriver - Google Search"));
  }
}
```

## Desired Capabilities

See the DesiredCapabilities page for [Safari specific](DesiredCapabilities#Safari_specific.md) capability options.

## Debugging

Sometimes, a test may hang after sending a command to the SafariDriver. This usually indicates something blew up. Thankfully, the SafariDriver is quite chatty.

First open the WebKit inspector on the page under test and check the console output. The console is cleared each time a page is loaded, so you'll only be able to see the logs for the most recent injected script. You can select the injected script and set break points inside the inspector, but it is sandboxed from the page, so you can't play with it using the inspector's REPL.

If you're using a copy of the extension [built from source](SafariDriverInternals.md), you can also check the logs for the global page:  Develop > Show Extension Builder. Select the WebDriver extension, and click "Inspect global page." Again, the SafariDriver is super chatty, so you should see what went wrong on the console.  You can set script break points for the injected page and interact with it using the REPL.

## Known Issues

All known issues with the SafariDriver may be found on our [issue tracker](http://code.google.com/p/selenium/issues/list?can=1&q=browser%3DSafari+component%3DWebDriver+status%3ANew%2CAccepted%2CWorkingAsIntended%2CWontFix%2CNotFeasible&colspec=ID+Stars+Type+Status+Priority+Owner+Summary+Browser+Component&cells=tiles).

## Reporting Bugs

If you think you've found a bug, please open a [bug](http://code.google.com/p/selenium/issues/list) on our issue tracker.  When filing a bug, please adhere to the following guidelines:
  1. Prefix your summary line with "`[SafariDriver]`."  For example: "`[SafariDriver] Unable to execute JavaScript`".
  1. Include the injected script's log output (see [above](#Debugging.md)).
  1. Include the global page's log output (again, see [above](#Debugging.md)).
  1. Include a [reduced](http://www.webkit.org/quality/reduction.html) test case for your bug.
  1. Avoid filing duplicate bugs for the aforementioned [known issues](#Known_Issues.md).

## SafariDriver Internals

Refer to the SafariDriverInternals wiki page.