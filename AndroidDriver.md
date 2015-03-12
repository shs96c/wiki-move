# REMOVED FROM THE PROJECT

## Use [Selendroid](http://selendroid.io/webview.html) instead.




# Introduction

Android WebDriver allows to run automated end-to-end tests that ensure your site works correctly when viewed from the Android browser. Android WebDriver supports all core WebDriver APIs, and in addition to that it supports mobile spacific and HTML5 APIs. Android WebDriver models many user interactions such as finger taps, flicks, finger scrolls and long presses. It can rotate the display and interact with HTML5 features such as local storage, session storage and application cache.

We try to stay as close as possible to what the user interaction with the browser is. To do so, Android WebDriver runs the tests against a WebView (rendering component used by the Android browser) configured like the Android browser. To interact with the page Android WebDriver uses native touch and key events. To query the DOM, it uses the JavaScript Atoms libraries.

## Supported Platforms
  * The [current apk](http://code.google.com/p/selenium/downloads/list) will only work with Gingerbread (2.3.x), Honeycomb (3.x), Ice Cream Sandwich (4.0.x) and later.
    * Note that there is an **emulator bug on Gingerbread** that might cause WebDriver to crash.
  * The last version to support Froyo (2.2) is [2.16](http://code.google.com/p/selenium/downloads/detail?name=android-server-2.16.apk).

## Useful Related Links
  * [Android WebDriver on Android Blog](http://android-developers.blogspot.com/2011/10/introducing-android-webdriver.html)
  * [Android WebDriver on Google Open Source Blog](http://google-opensource.blogspot.com/2011/10/test-your-mobile-web-apps-with.html)
# Get Started
## Install the Android SDK
Download the [Android SDK](http://developer.android.com/sdk/index.html), and unpack it to ~/android\_sdk/. NOTE: The location of the Android SDK must exist in `../android_sdk`, relative to the directory containing the Selenium repository.

## Setup the Environment
Android WebDriver test can run on emulators or real devices for phone and tablets.

### Setup the Emulator

To create an emulator, you can use the [graphical interface](http://developer.android.com/guide/developing/devices/managing-avds.html) provided by the Android SDK, or the [command line](http://developer.android.com/guide/developing/devices/emulator.html). Below are instructions for using the command line.

First, let's create an Android Virtual Device (AVD):
```
$cd ~/android_sdk/tools/
$./android create avd -n my_android -t 12 -c 100M
```

-n: specifies the name of the AVD.

-t: specifies the platform target. For an exhaustive list of targets, run:
```
./android list targets
```
Make sure the target level you selected corresponds to a supported SDK platform.

-c: specifies the SD card storage space.

When prompted "Do you wish to create a custom hardware profile `[`no`]`" enter "no".

**Note: An Android emulator bug prevents WebDriver from running on emulators on platforms 2.3 (Gingerbread). Please refer to [AndroidDriver#Supported\_Platforms](AndroidDriver#Supported_Platforms.md) for more information**

Now, start the emulator. This can take a while, but take a look at the FAQ below for tips on how to improve performance of the emulator.
```
$./emulator -avd my_android &
```

### Setup the Device
Simply connect your Android device through USB to your machine.


Now that we're done with the test environment setup, let's take a look at how to write tests. There are two options available to you to run Android WebDriver tests:
  * Using the [remote WebDriver server](http://code.google.com/p/selenium/wiki/RemoteWebDriver)
  * Using the [Android test framework](http://developer.android.com/guide/topics/testing/testing_android.html)
Here is a comparaison of both approach to help you choose which one suits you best.

|  **Android WebDriver using the remote server** | **Android WebDriver using the Android test framework** |
|:-----------------------------------------------|:-------------------------------------------------------|
| Tests can be written in any supported language binding (Java, Python, Ruby, etc.) | Tests can only be written in Java                      |
| Runs slower because every command makes extra RPCs (HTTP requests/responses) | Runs fasters because the tests are on the device       |
| Ideal if you use the same WebDriver tests on other browsers | Ideal if you don't use the same tests on other browsers and if you already use the Android test framework |

## Using the Remote Server

This approach has a client and a server component. The client consists of your typical Junit tests that you can run from your favorite IDE or through the command line. The server is an Android application that contains an HTTP server. When you run the tests, each WebDriver command will make a RESTful  HTTP request to the server using JSON according to a [well defined protocol](http://code.google.com/p/selenium/wiki/JsonWireProtocol). The remote server will delegate the request to Android WebDriver, and will then return a response.


### Install the WebDriver APK
Every device or emulator has a serial ID. Run this command to get the serial ID of the device or emulator you want to use:
```
$~/android_sdk/platform-tools/adb devices
```

Download the Android server from our [downloads page](http://code.google.com/p/selenium/downloads/list). To install the application do:
```
$./adb -s <serialId> -e install -r  android-server.apk
```

Make sure you are allowing installation of application not coming from Android Market. Go to Settings -> Applications, and check "Unknown Sources".

Start the Android WebDriver application through the UI of the device or by running this command:
```
$./adb -s <serialId> shell am start -a android.intent.action.MAIN -n org.openqa.selenium.android.app/.MainActivity
```

You can start the application in debug mode, which has more verbose logs by doing:
```
$./adb -s <serialId> shell am start -a android.intent.action.MAIN -n org.openqa.selenium.android.app/.MainActivity -e debug true
```

Now we need to setup the port forwarding in order to forward traffic from the host machine to the emulator.
In a terminal type:
```
$./adb -s <serialId> forward tcp:8080 tcp:8080
```

This will make the android server available at `http://localhost:8080/wd/hub/status` from the host machine. (The wiki page [DeveloperTips](http://code.google.com/p/selenium/wiki/DeveloperTips#Manually_interacting_with_RemoteWebDriverServer) has some more information on this.)

You're now ready to run the tests. Let's take a look at some code.

### Run the Tests
```
import junit.framework.TestCase;

import org.openqa.selenium.By;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.android.AndroidDriver;

public class OneTest extends TestCase {

  public void testGoogle() throws Exception {
    WebDriver driver = new AndroidDriver();
    
    // And now use this to visit Google
    driver.get("http://www.google.com");
    
    // Find the text input element by its name
    WebElement element = driver.findElement(By.name("q"));
    
    // Enter something to search for
    element.sendKeys("Cheese!");
    
    // Now submit the form. WebDriver will find the form for us from the element
    element.submit();
    
    // Check the title of the page
    System.out.println("Page title is: " + driver.getTitle());
    driver.quit();
  }
}
```

To compile and run this example you will need the selenium-java-X.zip (client side piece of selenium).
Download the selenium-java-X.zip from our [download page](http://code.google.com/p/selenium/downloads/list), unzip and include all jars in your IDE project. For Eclipse, right click on project -> Build Path -> Configure Build Path -> Libraries -> Add External Jars.


## Using the Android Test Framework

Is really not recommended. None of the core development team have experience with this, and very few people in the community are using the android driver this way.


# FAQ

## My tests are slow, what can I do?
  * Avoid looking up elements by XPath, it is by far more expensive than using IDs or name.
  * Keep the same emulator instance for all tests. Starting an Android emulator is an expensive operation which can take up to 2 minute depending on your machine specifications. Do this once for your entire test suite. If you need a fresh instance of the driver call WebDriver.quit() which flushes the WebView cache, cookies, user data, etc.
  * Prefer window handles to window names (e.g. String current = driver.getWindowHandle(); driver.switchTo().window(current);).  Switching to a window by it's name property is more expensive because it requires injecting Javascript in every open window to fetch the window's name. While handles are saved in map which provides really fast lookup times.

## The emulator is too slow to start
The Android emulator can take up to 2 or 3 minutes to start.  Consider using the [Android snapshot](http://tools.android.com/recent/emulatorsnapshots) feature, which allows you to start an emulator in less than 2 seconds!

You can speed up the tests by disabling animations, audio, skins, and even by running in headless mode. To do so start  the emulator with the options --no-boot-anim, --no-audio, --noskin, and --no-window.

If you start the emulator with -no-window, you can launch the WebDriver app with this command:
```
$adb shell am start -a android.intent.action.MAIN -n org.openqa.selenium.android.app/.MainActivity
```

Or in debug mode:
```
 $adb shell am start -a android.intent.action.MAIN -n org.openqa.selenium.android.app/.MainActivity -e debug true
```

## Geo location does not work
  * Remember to set the following settings on your device: Settings -> Applications -> Development -> Check "USB debugging", "Stay Awake" and "Allow mock locations"

## ADB does not find the emulator or device
  * Sometimes there are issues connecting to the device / emulator in which case you can restart adb by doing:
```
$ adb kill-server
$ adb start-server
```

## How to start the HTTP server Jetty
Jetty is started by the Android WebDriver application when launched.

## I get "Page not Available"
Sometimes the Android emulator does not detect the connection of the host machine it runs on. The solution in this case is to restart the emulator.

## Android WebDriver fails to load HTTPS pages
You can enable accepting all HTTPS certs by setting a capability as follow:
```
DesiredCapabilities caps = DesiredCapabilities.android();
caps.setCapability(CapabilityType.ACCEPT_SSL_CERTS, true);

AndroidDriver driver = new AndroidDriver(caps);
```

## Can't talk to the emulator or device
Sometimes executing adb commands will fail because the adb server fails to talk to the device or emulator. To fix this kill the adb server and restart it:
```
$adb kill-server

$adb start-server
```

## Inconsistent certificates when installing the apk
You may see the following error when trying to install an apk:
```
Failure [INSTALL_PARSE_FAILED_INCONSISTENT_CERTIFICATES]
```

This means that the certificates used to sign the apk are different from the certificate used with the already installed apk. To fix this, unistall the current apk:

```
$adb uninstall org.openqa.selenium.android.app/.MainActivity
```

You can also uninstall an app manually, by opening the Settings -> Applications -> select WebDriver -> Uninstall.

## Can't access the Android WebDriver server from another machine

If you want to access the Android WebDriver server from another machine (or an interface which isn't localhost), do the following after doing the port forwarding:

```
# Instal socat, one time setup
$sudo apt-get install socat

$socat TCP-LISTEN:8081,fork TCP:localhost:8080
```

Now the Android WebDriver server can be accessed from http://hostname:8081/wd/hub from any machine or network interface.

### X compatibility
Note: The android emulator is not compatible with xvfb, and will not run in it.  If you need a headless X environment, run your emulator with the -no-window option, or use tightvncserver.