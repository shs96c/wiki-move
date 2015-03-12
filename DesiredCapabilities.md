# Introduction

Not all server implementations will support every WebDriver feature. Therefore, the client and server should use JSON objects with the properties listed below when describing which features a user [requests](JsonWireProtocol#POST_/session.md) that a session support. If a session cannot support a capability that is requested in the desired capabilities, no error is thrown; a read-only capabilities object is returned that indicates the capabilities the session actually supports.

### Used by the selenium server for browser selection

| **Key** | **Type** | **Description** |
|:--------|:---------|:----------------|
| browserName | string   | The name of the browser being used; should be one of `{android|chrome|firefox|htmlunit|internet explorer|iPhone|iPad|opera|safari}`. |
| version | string   | The browser version, or the empty string if unknown. |
| platform | string   | A key specifying which platform the browser should be running on. This value should be one of `{WINDOWS|XP|VISTA|MAC|LINUX|UNIX|ANDROID}`. When requesting a new session, the client may specify `ANY` to indicate any available platform may be used. For more information see [[GridPlatforms](GridPlatforms.md)] |


## Read-only capabilities

| **Key** | **Type** | **Description** |
|:--------|:---------|:----------------|
| takesScreenshot | boolean  | Whether the session supports taking screenshots of the current page. |
| handlesAlerts | boolean  | Whether the session can interact with modal popups, such as `window.alert` and `window.confirm`. |
| cssSelectorsEnabled | boolean  | Whether the session supports CSS selectors when searching for elements. |


## Read-write capabilities

| **Key** | **Type** | **Description** |
|:--------|:---------|:----------------|
| javascriptEnabled | boolean  | Whether the session supports executing user supplied JavaScript in the context of the current page  (only on HTMLUnitDriver). |
| databaseEnabled | boolean  | Whether the session can interact with database storage. |
| locationContextEnabled | boolean  | Whether the session can set and query the browser's location context. |
| applicationCacheEnabled | boolean  | Whether the session can interact with the application cache. |
| browserConnectionEnabled | boolean  | Whether the session can query for the browser's connectivity and disable it if desired. |
| webStorageEnabled | boolean  | Whether the session supports interactions with [storage objects](http://www.w3.org/TR/2009/WD-webstorage-20091029/). |
| acceptSslCerts | boolean  | Whether the session should accept all SSL certs by default. |
| rotatable | boolean  | Whether the session can rotate the current page's current layout between portrait and landscape orientations (only applies to mobile platforms). |
| nativeEvents | boolean  | Whether the session is capable of generating native events when simulating user input. |
| proxy   | proxy object | Details of any proxy to use. If no proxy is specified, whatever the system's current or default state is used. The format is specified under Proxy JSON Object. |
| unexpectedAlertBehaviour | string   | What the browser should do with an unhandled alert before throwing out the UnhandledAlertException. Possible values are "accept", "dismiss" and "ignore" |
| elementScrollBehavior | integer  | Allows the user to specify whether elements are scrolled into the viewport for interaction to align with the top (0) or bottom (1) of the viewport. The default value is to align with the top of the viewport. Supported in IE and Firefox (since 2.36) |


# RemoteWebDriver specific
| webdriver.remote.sessionid | string | WebDriver session ID for the session. Readonly and only returned if the server implements a server-side webdriver-backed selenium. |
|:---------------------------|:-------|:-----------------------------------------------------------------------------------------------------------------------------------|
| webdriver.remote.quietExceptions | boolean | Disable automatic screnshot capture on exceptions. This is False by default.                                                       |

# Grid-specific
| path | string | ??? Path to route request to, or maybe listen on. |
|:-----|:-------|:--------------------------------------------------|
| seleniumProtocol | string | Which protocol to use. Accepted values: WebDriver, Selenium. |
| maxInstances | integer | Maximum number of instances to allow to connect to grid, |
| environment | string | ??? Possible duplicate of browserName? See RegistrationRequest. |

# Selenium RC (1.0) only
| proxy\_pac | DoNotUseProxyPac | Legacy proxy mechanism. Do not use. |
|:-----------|:-----------------|:------------------------------------|
| commandLineFlags | string           | Flags to pass to browser command line. |
| executablePath | string           | Path to browser executable.         |
| timeoutInSeconds | long integer     | Timeout to wait for the browser to launch, in seconds. |
| onlyProxySeleniumTraffic | boolean          | Whether to only proxy selenium traffic. See browserlaunchers.Proxies |
| avoidProxy | boolean          | ??? See browserlaunchers.Proxies    |
| proxyEverything | boolean          | ??? See browserlaunchers.Proxies    |
| proxyRequired | boolean          | ??? See browserlaunchers.Proxies    |
| browserSideLog | boolean          | ??? See AbstractBrowserLauncher.    |
| optionsSet | boolean          | ??? See BrowserOptions.             |
| singleWindow | boolean          | Whether to enable single window mode. |
| dontInjectRegex | javascript RegExp | Regular expression that proxy injection mode can use to know when to bypss injection. Ignored if not in proxy injection mode. |
| userJSInjection | boolean          | ??? Whether to inject user JS. Ignored if not in proxy injection mode. |
| userExtensions | string           | Path to a JavaScript file that will be loaded into selenium. |

## Selenese-Backed-WebDriver specific
| selenium.server.url | string | URL of Selenium server to use, to back this WebDriver |
|:--------------------|:-------|:------------------------------------------------------|

# Browser Specific Capabilities

## Opera specific
| opera.binary | string | Path to Opera binary |
|:-------------|:-------|:---------------------|
| opera.guess\_binary\_path | boolean | Whether to guess the path to Opera if it isn't set in opera.binary |
| opera.no\_restart | boolean | Whether to restart Opera |
| opera.product | string | Which Opera product we're using, e.g. "desktop", "core" |
| opera.no\_quit | boolean | Whether to quit Opera when OperaDriver is shut down. If enabled, it will keep the browser running after the driver is shut down. |
| opera.autostart | boolean | Whether to auto-start the Opera binary. If false, OperaDriver will wait for a connection from the browser. Go to "opera:debug", enter the correct port number, and hit "Connect" to connect manually. |
| opera.display | integer | Which X display to use (`*`nix only) |
| opera.idle   | boolean | Whether to use Opera's alternative implicit wait implementation. It will use an in-browser heuristic to guess when a page has finished loading, allowing us with great accuracy tell whether there are any planned events in the document. This functionality is useful for very simple test cases, but not designed for real-world testing. It is disabled by default. |
| opera.profile | object|string | Directory to use for the Opera profile. If an OperaProfile object, that will be used when starting opera. If null a random temporary directory is used. If "", an empty string, then the default .autotest profile directory will be used (for backwards compatibility with Opera < 11.60). |
| opera.launcher | string | Path to the launcher binary to use. The launcher is a gateway between OperaDriver and the Opera browser, and is being used for controlling the binary and taking external screenshots. If left blank, OperaDriver will use the launcher supplied with the package. |
| opera.port   | integer | The port to Opera should connect to. 0 = Random, -1 = Opera default (for use with Opera < 11.60) |
| opera.host   | string | The host Opera should connect to. Unless you're starting Opera manually you won't need this. |
| opera.arguments | string | Arguments to pass to Opera, separated by spaces. See `opera -help` for available command-line switches. |
| opera.logging.file | string | Where to send the output of the logging. Default is to not write to file. |
| opera.logging.level | string |  How verbose the logging should be. Available levels are: SEVERE (highest value), WARNING, INFO, CONFIG, FINE, FINER, FINEST (lowest value), ALL. Default is INFO. TODO: These seem the wrong way around. Also what about off. Also, unify with firefox loggingPrefs. |

## Chrome specific
See [Chrome capabilities](https://sites.google.com/a/chromium.org/chromedriver/capabilities)

## Firefox specific
### WebDriver
| firefox\_profile | string | Base64-encoded profile.  TODO: Document format |
|:-----------------|:-------|:-----------------------------------------------|
| loggingPrefs     | LoggingPreferences object | Preferences for logging                        |
| firefox\_binary  | string | Path to firefox binary file to use.            |

### RC
| mode | string | Mode for browser. Possible values: chrome, proxyInjection, proxy. Defaults to chrome, if not set. proxyInjection requires -proxyInjection to be passed to server command line. |
|:-----|:-------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| captureNetworkTraffic | boolean | Whether to capture network traffic.                                                                                                                                            |
| addCustomRequestHeaders | boolean | Whether to add custom request headers.                                                                                                                                         |
| trustAllSSLCertificates | boolean | Whether to trust all SSL certificates.                                                                                                                                         |
| changeMaxConnections | boolean | ??? See FirefoxChromeLauncher.                                                                                                                                                 |
| firefoxProfileTemplate | string | ??? See FirefoxChromeLauncher.                                                                                                                                                 |
| profile | string | ??? See FirefoxChromeLauncher                                                                                                                                                  |

## IE specific
### WebDriver
| ignoreProtectedModeSettings | boolean | Whether to skip the protected mode check. If set, tests may become flaky, unresponsive, or browsers may hang. If not set, and protected mode settings are not the same for all zones, an exception will be thrown on driver construction. Only "best effort" support is provided when using this capability. |
|:----------------------------|:--------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ignoreZoomSetting           | boolean | Indicates whether to skip the check that the browser's zoom level is set to 100%. Value is set to false by default.                                                                                                                                                                                          |
| initialBrowserUrl           | string  | Allows the user to specify the initial URL loaded when IE starts. Intended to be used with `ignoreProtectedModeSettings` to allow the user to initialize IE in the proper Protected Mode zone. Using this capability may cause browser instability or flaky and unresponsive code. Only "best effort" support is provided when using this capability. |
| enablePersistentHover       | boolean | Determines whether persistent hovering is enabled (true by default). Persistent hovering is achieved by continuously firing mouse over events at the last location the mouse cursor has been moved to.                                                                                                       |
| enableElementCacheCleanup   | boolean | Determines whether the driver should attempt to remove obsolete elements from the element cache on page navigation (true by default). This is to help manage the IE driver's memory footprint, removing references to invalid elements.                                                                      |
| requireWindowFocus          | boolean | Determines whether to require that the IE window have focus before performing any user interaction operations (mouse or keyboard events). This capability is false by default, but delivers much more accurate native events interactions.                                                                   |
| browserAttachTimeout        | integer | The timeout, in milliseconds, that the driver will attempt to locate and attach to a newly opened instance of Internet Explorer. The default is zero, which indicates waiting indefinitely.                                                                                                                  |
| ie.forceCreateProcessApi    | boolean | Forces launching Internet Explorer using the CreateProcess API. If this option is not specified, IE is launched using the IELaunchURL, if it is available. For IE 8 and above, this option requires the TabProcGrowth registry value to be set to 0.                                                         |
| ie.browserCommandLineSwitches | string  | Specifies command-line switches with which to launch Internet Explorer. This is only valid when used with the forceCreateProcess.                                                                                                                                                                            |
| ie.usePerProcessProxy       | boolean | When a proxy is specified using the proxy capability, this capability sets the proxy settings on a per-process basis when set to true. The default is false, which means the proxy capability will set the system proxy, which IE will use.                                                                  |
| ie.ensureCleanSession       | boolean | When set to true, this capability clears the cache, cookies, history, and saved form data. When using this capability, be aware that this clears the cache for all running instances of Internet Explorer, including those started manually.                                                                 |
| logFile                     | string  | The path to file where server should write log messages to. By default it writes to stdout.                                                                                                                                                                                                                  |
| logLevel                    | string  | The log level used by the server. Valid values are TRACE, DEBUG, INFO, WARN, ERROR, and FATAL. Defaults to FATAL if not specified.                                                                                                                                                                           |
| host                        | string  | The address of the host adapter on which the server will listen for commands.                                                                                                                                                                                                                                |
| extractPath                 | string  | The path to the directory used to extract supporting files used by the server. Defaults to the TEMP directory if not specified.                                                                                                                                                                              |
| silent                      | boolean | Suppresses diagnostic output when the server is started.                                                                                                                                                                                                                                                     |
| ie.setProxyByServer         | boolean | Defines used proxy setter. False means WindowsProxyManager will be used for setting proxy settings. True means IEDriverServer will be used for setting proxy settings. Currently it's False by default. In next releases it will be set to True by default and removed.                                      |

### RC
| mode | string | Mode for browser. Possible values: iehta, proxyInjectionMode, proxy. Defaults to iehta if not set. proxyInjection requires -proxyInjection to be passed to server command line. |
|:-----|:-------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| killProcessesByName | boolean | Whether to try to kill processes by name, instead (or addition) to killing processes we happen to have handles to.                                                              |
| honorSystemProxy | boolean | Whether to honor the system proxy.                                                                                                                                              |
| ensureCleanSession | boolean | Whether to make sure the session has no cookies or temporary internet files on Windows. I believe this is passed to the IEDriver as well, but ignored by it.                    |

## Safari specific
### WebDriver
| **Key** | **Type** | **Description** |
|:--------|:---------|:----------------|
| safari.options | object   | A map of configuration options, as enumerated below. |

Available options:

| **Key** | **Type** | **Description** |
|:--------|:---------|:----------------|
| cleanSession | boolean  | Whether to make sure the session has no cookies, cache entries, local storage, or databases. |


### RC
| mode | string | Mode for browser. Possible values: filebased, proxyInjectionMode, proxy. Defaults to filebased. Note: filebased does not work on newer Safaris. proxyInjection requires -proxyInjection to be passed to server command line. |
|:-----|:-------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| honorSystemProxy | boolean | Whether to honour the sysem proxy.                                                                                                                                                                                           |
| ensureCleanSession | boolean | Whether to make sure the session has no cookies, cache entries. And that any registry and proxy settings are restored after the session.                                                                                     |

# Object structures

## Proxy JSON Object
A JSON object describing a Proxy configuration.

| **Key** | **Type** | **Description** |
|:--------|:---------|:----------------|
| proxyType | string   | (Required) The type of proxy being used. Possible values are: **direct** - A direct connection - no proxy in use, **manual** - Manual proxy settings configured, e.g. setting a proxy for HTTP, a proxy for FTP, etc, **pac** - Proxy autoconfiguration from a URL, **autodetect** - Proxy autodetection, probably with WPAD, **system** - Use system settings |
| proxyAutoconfigUrl | string   | (Required if proxyType == **pac**, Ignored otherwise) Specifies the URL to be used for proxy autoconfiguration. Expected format example: http://hostname.com:1234/pacfile |
| ftpProxy, httpProxy, sslProxy, socksProxy | string   | (Optional, Ignored if proxyType != **manual**) Specifies the proxies to be used for FTP, HTTP, HTTPS and SOCKS requests respectively. Behaviour is undefined if a request is made, where the proxy for the particular protocol is undefined, if proxyType is **manual**. Expected format example: hostname.com:1234 |
| socksUsername | string   | (Optional, Ignored if proxyType != **manual** and socksProxy is not set) Specifies SOCKS proxy username. |
| socksPassword | string   | (Optional, Ignored if proxyType != **manual** and socksProxy is not set) Specifies SOCKS proxy password. |
| noProxy | string   | (Optional, Ignored if proxyType != **manual**) Specifies proxy bypass addresses. Format is driver specific. |

## LoggingPreferences JSON object
A JSON object describing the logging level of different components in the browser, the driver, or any intermediary WebDriver servers.

Available values for most loggers are "OFF", "SEVERE", "WARNING", "INFO", "CONFIG", "FINE", "FINER", "FINEST", "ALL".

This produces a JSON object looking something like: `{"loggingPrefs": {"driver": "INFO", "server": "OFF", "browser": "FINE"`}}.

See the documentation of each driver for what browser specific logging components are available.

| **Key** | **Type** | **Description** |
|:--------|:---------|:----------------|
| component | string   | How verbose the logging should be. |

## FirefoxProfile settings
Preferences accepted by the FirefoxProfile with special meaning, in the WebDriver API:
| **Key** | **Type** | **Description** |
|:--------|:---------|:----------------|
| webdriver\_accept\_untrusted\_certs | boolean  | Whether to trust all SSL certificates. TODO: Maybe in some way different to the acceptSslCerts or trustAllSSLCertificates capabilities. |
| webdriver\_assume\_untrusted\_issuer | boolean  | Whether to trust all SSL certificate issuers. TODO: Maybe in some way different to the acceptSslCerts or trustAllSSLCertificates capabilities. |
| webdriver.log.driver | string   | Level at which to log FirefoxDriver logging statements to a temporary file, so that they can be retrieved by a getLogs command. Available options; DEBUG, INFO, WARNING, ERROR, OFF. Defaults to OFF. |
| webdriver.log.file | string   | Path to file to which to copy firefoxdriver logging output. Defaults to no file (like /dev/null). |
| webdriver.load.strategy | string   | Experimental API. Defines different strategies for how long to wait until a page is loaded. Values: unstable, conservative. Defaults to conservative. |
| webdriver\_firefox\_port | integer  | Port to listen on for WebDriver commands. Defaults to 7055. |