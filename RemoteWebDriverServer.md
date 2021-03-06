The RemoteWebDriver is composed of two pieces: a client and a server. The client is your WebDriver test and the server is simply a Java servlet, which can be hosted in any modern JEE app server. The server will always run on the machine with the browser you want to test. There are two ways to user the server: command line or configured in code.

## Starting the Server from The Command Line

Once you have downloaded selenium-server-standalone-{VERSION}.jar place it on the computer with the browser you want to test. Then from the directory with the jar run the following

```
java -jar selenium-server-standalone-{VERSION}.jar
```

## Considerations when running server
The caller is expected to terminate each session properly, calling either Selenium#stop() or WebDriver#quit.

The selenium-server keeps in-memory logs for each ongoing session, which are cleared when Selenium#stop() or WebDriver#quit is called. If you forget to terminate these sessions your server may leak memory. If you keep extremely long-running sessions you will probably need to stop/quit every now and then (or increase memory with -Xmx jvm option)

### Timeouts (from version 2.21)
The server has two different timeouts, which can be set as follows:
```
java -jar selenium-server-standalone-{VERSION}.jar -timeout=20 -browserTimeout=60
```

| browserTimeout | Controls how long the browser is allowed to hang (value in seconds)|
|:---------------|:-------------------------------------------------------------------|
| timeout        | Controls how long the client is  is allowed to be gone before the session is reclaimed (value in seconds)|

System property "selenium.server.session.timeout" is no longer supported as of 2.21.

Please note that the "browserTimeout" is intended as a backup timeout mechanism when the ordinary timeout mechanism fails, which should be used mostly in grid/server environments to ensure that crashed/lost processes do not stay around for too long, polluting the runtime environment.

## Configuring the Server in Code

In theory, the process is as simple as mapping the "DriverServlet" to a URL, but it's also possible to host the page in a lightweight container, such as Jetty configured entirely in code. Steps to do this follow.

### Details

  1. Download the "selenium-server.zip" and unpack. Put the JARs on the CLASSPATH
  1. Create a new class called "AppServer". Here, I'm using Jetty, so you'll need to download that as well:
```
import org.mortbay.jetty.Connector;
import org.mortbay.jetty.Server;
import org.mortbay.jetty.nio.SelectChannelConnector;
import org.mortbay.jetty.security.SslSocketConnector;
import org.mortbay.jetty.webapp.WebAppContext;

import javax.servlet.Servlet;
import java.io.File;

import org.openqa.selenium.remote.server.DriverServlet;

public class AppServer {
  private Server server = new Server();

  public AppServer() throws Exception {
    WebAppContext context = new WebAppContext();
    context.setContextPath("");
    context.setWar(new File("."));
    server.addHandler(context);

    context.addServlet(DriverServlet.class, "/wd/*");

    SelectChannelConnector connector = new SelectChannelConnector();
    connector.setPort(3001);
    server.addConnector(connector);

    server.start();
  }
}
```

## Using the RemoteWebDriver
See the RemoteWebDriver client page for more information on using the client.