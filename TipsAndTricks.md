# Tips and Tricks

## Using Drag and Drop

It may not be immediately obvious, but if you're using a browser that supports it , you can use Action classes and then it's easy to do drag and drop:

```
 Actions builder = new Actions(driver);

   Action dragAndDrop = builder.clickAndHold(someElement)
       .moveToElement(otherElement)
       .release(otherElement)
       .build();

   dragAndDrop.perform();
   
```

Currently, only the FirefoxDriver supports this, but you should also expect support for the InternetExplorerDriver too.

## Changing the user agent

This is easy with the FirefoxDriver:

```
FirefoxProfile profile = new FirefoxProfile();
profile.setPreference("general.useragent.override", "some UA string");
WebDriver driver = new FirefoxDriver(profile);
```

## Tweaking an existing Firefox profile

Suppose that you wanted to modify the user agent string (as above), but you've got a tricked out Firefox profile that contains dozens of useful extensions. There are two ways to obtain this profile. Assuming that the profile has been created using Firefox's profile manager ("firefox -ProfileManager"):

```
ProfilesIni allProfiles = new ProfilesIni();
FirefoxProfile profile = allProfiles.getProfile("WebDriver");
profile.setPreferences("foo.bar", 23);
WebDriver driver = new FirefoxDriver(profile);
```

Alternatively, if the profile isn't already registered with Firefox:

```
File profileDir = new File("path/to/top/level/of/profile");
FirefoxProfile profile = new FirefoxProfile(profileDir);
profile.setPreferences(extraPrefs);
WebDriver driver = new FirefoxDriver(profile);
```

## Enabling features that are disabled by default in Firefox

Native events is such a feature: It is disabled by default for Firefox on Linux as it may cause tests which open many windows in parallel to be unreliable. However, native events work quite well otherwise and are essential for some of the new actions of the Advanced User Interaction. To enable them:

```
FirefoxProfile profile = new FirefoxProfile();
profile.setEnableNativeEvents(true);
WebDriver driver = new FirefoxDriver(profile);
```

## How to set language in profile
```
profile.setPreference( "intl.accept_languages", "no,en-us,en" ); 
```

## How to find profile keys
Look in _prefs.js_ of your Firefox profile.

## How to find Firefox profile
Goto (url) about:support or FireFox Help menu / Troubleshooting information