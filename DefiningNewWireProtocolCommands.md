# Defining A New Wire Protocol Command

Defining a new command in WebDriver's [remote wire protocol](JsonWireProtocol.md) can seem like a daunting task to new developers, but the process is really straight forward.  This page documents the steps one need follow to extend WebDriver's functionality.

## Update the Wire Protocol

The first step is to update the formal protocol [spec](JsonWireProtocol.md).  To ensure that everything is consistently formatted, you should use the provided script instead of editing the page through the UI:
```
# Set up your client.
$ svn co https://selenium.googlecode.com/svn/ --depth=empty wire_protocol
$ cd wire_protocol
$ svn update --depth=infinity ./wiki
$ svn update --depth=files ./trunk

# Edit ./trunk/wire.py in your favorite editor.
$ vi ./trunk/wire.py

# Apply and review your changes.
$ python ./trunk/wire.py > ./wiki/JsonWireProtocol.wiki
$ svn diff ./wiki/JsonWireProtocol.wiki

# Commit everything together so changes can be easily tracked in the logs.
$ svn commit ./trunk/wire.py ./wiki/JsonWireProtocol.wiki
```

## Implement the Command for Each Driver

The details for updating each driver is beyond the scope of this document, so we shall only cover the FirefoxDriver as an illustrative example.

  1. Configure the mapping from resource URL to command handler in `//firefox/src/extension/components/dispatcher.js`
```
Dispatcher.prototype.init_ = function() {
  // ...

  this.bind_('/session/:sessionId/timeouts/implicitly_wait').
      on(Request.Method.POST, Dispatcher.executeAs('implicitlyWait'));

  // ...
};
```
  1. Implement the individual command handlers in `//firefox/src/extension/components/firefoxDriver.js`
```
FirefoxDriver.prototype.implicitlyWait = function(response, parameters) {
  response.session.setImplicitWait(parameters.ms);
  response.send();
};
```

## Update the RemoteWebDriver Clients

### Java

  1. Define a constant for the command in `org.openqa.selenium.remote.DriverCommand`
```
  String IMPLICITLY_WAIT = "implicitlyWait";
```
  1. Map this constant to the correct resource URL in `org.openqa.selenium.remote.HttpCommandExecutor`
```
  nameToUrl = ImmutableMap.<String, CommandInfo>builder()
      // ...
      .put(IMPLICITLY_WAIT, post("/session/:sessionId/timeouts/implicit_wait"))
      .build();
```
  1. Define the public facing API in `org.openqa.selenium.remote.RemoteWebDriver` (or `RemoteWebElement`, where appropriate)
```
public class RemoteWebDriver implements WebDriver {

  // ...

  public Options manage() {
    return new RemoteWebDriverOptions();
  }

  protected class RemoteWebDriverOptions implements Options {
    // ...
    public Timeouts timeouts() {
      return new RemoteTimeouts();
    }
  }

  protected class RemoteTimeouts implements Timeouts {
    public Timeouts implicitlyWait(long time, TimeUnit unit) {
      execute(DriverCommand.IMPLICITLY_WAIT, ImmutableMap.of("ms",
          TimeUnit.MILLISECONDS.convert(Math.max(0, time), unit)));
      return this;
    }
  }
}
```

### Ruby

TODO

### Python

TODO

### C#

TODO

## Update the RemoteWebDriver Server

  1. Define a new `org.openqa.selenium.remote.server.handler.WebDriverHandler`.
```
public class ImplicitlyWait extends WebDriverHandler implements JsonParametersAware {

  private volatile long millis;

  public ImplicitlyWait(DriverSessions sessions) {
    super(sessions);
  }

  public void setJsonParameters(Map<String, Object> allParameters) throws Exception {
    millis = ((Number) allParameters.get("ms")).longValue();
  }

  public ResultType call() throws Exception {
    getDriver().manage().timeouts().implicitlyWait(millis, TimeUnit.MILLISECONDS);

    return ResultType.SUCCESS;
  }

  @Override
  public String toString() {
    return String.format("[implicitly wait: %s]", millis);
  }
}
```
  1. Map the command handler to the appropriate resource URL in `org.openqa.selenium.remote.server.DriverServlet`
```
private void setupMappings(DriverSessions driverSessions, ServletLogTo logger) {
  // ...
  postMapper.bind("/session/:sessionId/timeouts/implicit_wait", ImplicitlyWait.class)
      .on(ResultType.SUCCESS, new EmptyResult());
}
```