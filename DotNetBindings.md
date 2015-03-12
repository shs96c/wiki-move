# Some details and docs about the .NET bindings

# The .NET bindings

This page is under construction


## Remote WebDriver

### Changing the timeout

In specific conditions (e.g. when browsers are served from a grid), it may take the Selenium server more than 60 seconds to provide a session. If this happens, a timeout exception will be thrown. To change the value of this wait, RemoteWebDriver takes a System.TimeSpan which is the command timeout for the HttpCommandExecutor. For more details, use the source, Luke:
https://code.google.com/p/selenium/source/browse/dotnet/src/WebDriver/Remote/RemoteWebDriver.cs#119