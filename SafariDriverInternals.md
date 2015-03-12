

# Introduction

This document provides a primer on the browser extension that powers the SafariDriver. It is not intended to be a full-fledged design document. When in doubt, refer to the source!


---


# Building the SafariDriver

  1. Sign up for Apple's (free) [Safari Developer Program](https://developer.apple.com/programs/safari/) and generate a signed certificate for the extension.
  1. Build the SafariDriver extension:
```
$ ./go safari
```
  1. Install the extension:
    1. Launch Safari
    1. Enable the Develop menu (Preferences > Advanced > Show Develop menu in menu bar)
    1. Open the Extension Builder (Develop > Show Extension Builder)
    1. Add a new extension: `$SELENIUM_CLIENT/build/javascript/safari-driver/SafariDriver.safariextension`
    1. Click Install

## Releasing the SafariDriver

To push a new release of the SafariDriver:
  1. Build and install the extension using the instructions above
  1. Open the Extension Builder and select the extension
  1. Click "Build Package ..."
  1. Save the package as SafariDriver.safariextz
  1. Copy your SafariDriver.safariextz file to `$SELENIUM_CLIENT/javascript/safari-driver/prebuilt/SafariDriver.safariextz`
  1. Commit your changes


---


# Components

The SafariDriver is implemented as a Safari browser extension.  There are three primary components within the extension: the [global extension](#Global_Extension.md), [injected script](#Injected_Script.md), and the [page script](#Page_Script.md).

## Global Extension

**Build target:** `//javascript/safari-driver:extension`<br>
<b>Code location:</b> <code>//javascript/safari-driver/extension</code><br>
<b>Namespace:</b> <code>safaridriver.extension</code>

The global extension is loaded once when Safari is first launched.  It is responsible for communicating with the WebDriver client, coordinating command execution with the injected script, and tracking opened windows.<br>
<br>
<h2>Injected Script</h2>

<b>Build target:</b> <code>//javascript/safari-driver:injected</code><br>
<b>Code location:</b> <code>//javascript/safari-driver/inject</code><br>
<b>Namespace:</b> <code>safaridriver.inject</code>

The injected script is injected into every web page. The script is injected after the document has been loaded, but before it has been parsed. The injected script shares a DOM with the web page, but runs in a separate JavaScript context.<br>
<br>
<table cellpadding='5' border='1' cellspacing='0' width='100%'>
<tbody>
<tr><td align='center'>
The injected script is only loaded for pages opened with <code>http://</code> or <code>https://</code>.<br />
<i><b>All other protocols are unsupported and will not be testable using the SafariDriver.</b></i>
</td></tr>
</tbody>
</table>

The injected script handles the execution of the majority of commands, as well as coordinating with selected frames.<br>
<br>
<h2>Page Script</h2>

<b>Build target:</b> <code>//javascript/safari-driver:page</code><br>
<b>Code location:</b> <code>//javascript/safari-driver/inject</code><br>
<b>Namespace:</b> <code>safaridriver.inject.page</code>

The page script is bundled as an extra resource in the SafariDriver. Each time an injected script is loaded, it will insert a new <code>SCRIPT</code> tag into the DOM that will load the bundled page script.  This script is used to execute certain commands in the page's context, such as <code>executeScript</code>, instead of the injected script's sandbox.<br>
<br>
<hr />

<h1>Communication Protocol</h1>

<pre><code>WebDriver Client &lt;---&gt; Global Extension &lt;---&gt; Injected Script &lt;---&gt; Page Script<br>
</code></pre>
A WebDriver client and the SafariDriver extension communicate using a WebSocket.  The extension is able to communicate with the injected script by dispatching messages using a <a href='http://developer.apple.com/library/safari/#documentation/UserExperience/Reference/ExtensionContentPageProxyClassRef/SafariWebPageProxy/SafariWebPageProxy.html#//apple_ref/doc/uid/TP40009778'>SafariWebPageProxy</a>. Similarly, the injected script is able to communicate with the extension using the <a href='http://developer.apple.com/library/safari/#documentation/UserExperience/Reference/SafariBrowserWindowTabProxyClassRef/SafariContentBrowserTabProxy/SafariContentBrowserTabProxy.html#//apple_ref/doc/uid/TP40009894'>!SafariContentBrowserTabProxy</a>. Finally, the injected and page scripts communicate with each other by using <code>window.postMessage</code>.<br>
<br>
By default, all messages exchanged between components are sent <b>asynchronously</b>. The injected script is able to submit a synchronous query to the extension using the tab proxy's <code>canLoad</code> method:<br>
<pre><code>  // Create a beforeload event, which is required by the canLoad function.<br>
  var stubEvent = document.createEvent('Events');<br>
  stubEvent.initEvent('beforeload', false, false);<br>
  var response = safari.self.tab.canLoad(stubEvent, 'Bob');<br>
  console.log(response);<br>
</code></pre>
The extension's response to a <code>canLoad</code> message must be set on the <a href='http://developer.apple.com/library/safari/#documentation/UserExperience/Reference/ExtensionMessageClassRef/SafariExtensionMessage/SafariExtensionMessage.html#//apple_ref/doc/uid/TP40009785'>SafariExtensionMessageEvent</a>'s <code>message</code> property in the event handler:<br>
<pre><code>  var browserWindow = safari.application.activeBrowserWindow;<br>
  var tab = browserWindow.activeTab;<br>
  tab.addEventListener('message', function(e) {<br>
    if (e.name == 'canLoad') {<br>
      e.message = 'Hello, ' + e.message + '. Nice to meet you';<br>
      e.stopPropagation();<br>
    }  // else not a synchronous message.<br>
  });<br>
</code></pre>
While the injected and page scripts run in different contexts, they share the same DOM and security domain, so they can communicate synchronously by dispatching a synthesized <code>MessageEvent</code> on the <code>window</code> object. Responses must be serialized to a string and passed using an attribute on a DOM element:<br>
<pre><code>  var messageEvent = document.createEvent('MessageEvent');<br>
  messageEvent.initMessageEvent('message', false, false, 'Bob',<br>
      window.location.origin, '0', window, null);<br>
  window.dispatchEvent(messageEvent);<br>
<br>
  // Read the response value from document.<br>
  var response = document.documentElement.getAttribute('response');<br>
  document.documentElement.removeAttribute('response');  // Clean-up.<br>
  console.log(response);<br>
</code></pre>

<h2>Message Format</h2>

Regardless of the mechanism used to pass messages, the SafariDriver uses a single JSON message protocol.<br>
<pre><code>{<br>
  /* A key identifying the message origin. The extension components use<br>
   * unique numeric constants, and WebDriver clients should _always_ use<br>
   * the value "webdriver".<br>
   */<br>
  "origin": "webdriver",<br>
<br>
  /* The type of message. */  <br>
  "type": "foo",<br>
<br>
  /* Additional fields vary by message type. Refer to the actual code for<br>
   * full documentation: //javascript/safari-driver/message<br>
   */<br>
}<br>
</code></pre>
There are only three messages types that a WebDriver client ever needs to worry about: the command, response, and connect messages.<br>
<br>
<h2>Communicating with the SafariDriver</h2>

The SafariDriver communicates with clients using WebSockets. While the SafariDriver browser extension maintains the client-end of the WebSocket extension, this section refers to it as the "server" end of the WebDriver API. Similarly, the term "client" refers to the user-facing WebDriver API that issues commands to the server.<br>
<br>
<h3>Command</h3>

<table><thead><th> <b>Key</b> </th><th> <b>Type</b> </th><th> <b>Description</b> </th></thead><tbody>
<tr><td> id         </td><td> string      </td><td> A random ID assigned to the command by the client; may be any string value. </td></tr>
<tr><td> name       </td><td> string      </td><td> The name of the command. Should be one of the values defined in <a href='http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/remote/DriverCommand.html'>org.openqa.selenium.remote.DriverCommand</a>. </td></tr>
<tr><td> parameters </td><td> <code>Object</code> </td><td> The command parameters as a JSON object. All parameter (key, value) pairs are consistent with those documented in the <a href='JsonWireProtocol#Command_Reference.md'>JSON wire protocol</a>. </td></tr></tbody></table>

Commands that rely on a URL parameter in the wire protocol should include those parameters in the parameter map using the same name as documented in the JsonWireProtocol. For instance, the wire protocol command<br>
<pre><code>POST /session/:sessionId/window/:windowHandle/size<br>
<br>
{"width":250, "height":250}<br>
</code></pre>
Should be encoded for the SafariDriver as<br>
<pre><code>{<br>
  "origin": "webdriver",<br>
  "type": "command",<br>
  "command": {<br>
    "id": "random-id-1234",<br>
    "name": "setWindowSize",<br>
    "parameters": {<br>
      "sessionId": "mySessionId",<br>
      "windowHandle": "current"<br>
    }<br>
  }<br>
}<br>
</code></pre>
The SafariDriver tracks sessions by WebSocket connection, so the <code>sessionId</code> parameter may actually be excluded from the parameter set.<br>
<br>
<h3>Response</h3>

The SafariDriver's response objects have a similar structure to commands:<br>
<pre><code>{<br>
  "origin": "webdriver",<br>
  "type": "response",<br>
  "id": "random-id-1234",<br>
  "response": {<br>
    "status": 0,<br>
    "value": null<br>
  }<br>
}<br>
</code></pre>
The <code>id</code> field in each response should echoe the ID sent with the corresponding command object.  The <code>response</code> field has the same format as specified by the <a href='JsonWireProtocol#Responses.md'>JSON wire protocol</a>.<br>
<br>
<h3>On Command/Response IDs</h3>

The SafariDriver will synchronize the execution of every command it receives. Thus, each response should always match the original command sent by the client. Upon receiving a response, the client should check that the ID matches the one sent with the original command. It is a catastrophic failure if these IDs do not match.<br>
<br>
<hr />

<h1>Control Flow</h1>

<h2>Connecting to the SafariDriver</h2>

Clients may connect to the SafariDriver by opening a page with the following:<br>
<pre><code>&lt;!DOCTYPE html&gt;<br>
&lt;script&gt;<br>
window.onload = function() {<br>
  window.postMessage({<br>
    'type': 'connect',<br>
    'origin': 'webdriver',<br>
    'url': 'ws://localhost:1234/wd'<br>
  }, '*');<br>
};<br>
&lt;/script&gt;<br>
</code></pre>
The posted message is intercepted by the injected script and passed along to the extension's global page, which will in turn establish the WebSocket connection with the requested server.<br>
<br>
Safari will not load extensions for <code>file://</code> URLs. In order to open the connection page, as shown above, it is recommended that this page be served by the WebSocket server maintained by the SafariDriver client.<br>
<br>
The SafariDriver supports multiple WebSocket connections. Each connection is treated as a separate session with its own timeout state (i.e., for implicit waits). All sessions share state for window and frame focus. Furthermore, the execution of commands is synchronized globally, across all sessions.<br>
<br>
<h2>Executing Commands</h2>

Once a connection has been established, the WebDriver client may start sending commands.  Command messages are received by the <code>safaridriver.extension.Server</code>. Some commands, like setting script timeouts or closing a window, can be handled directly by the extension.  Others must be sent to the injected script for execution (<code>safaridriver.extension.Tab.prototype.send</code>).<br>
<br>
<h3>Page Loading</h3>

Before a command can be sent to the injected script, the extension must wait for there to be an injected script <i>to send the command to</i>.  While Safari will emit navigation events whenever a new page is loaded, these events are not a sufficient indicator that the injected script has been initialized.  Therefore, each time the injected script is loaded and initialized, it sends a notification of the extension.  The extension will then execute the next queued command, if any.<br>
<br>
<h3>Frame Handling</h3>

Safari loads an injected script for each frame in a page. Furthermore, when the extension sends a message to a tab, it is broadcasted to every frame within that tab.  To compensate, the injected script in the SafariDriver keeps track of which frame currently has focus.<br>
<br>
The topmost frame in a tab (<code>safaridriver.inject.Tab</code>) always has focus after a page is loaded.   Whenever a command is received, the top frame first checks if it's a command that must be handled by top (e.g. <code>getWindowSize</code>), otherwise, it forwards the command to the currently focused tab for execution.<br>
<br>
As with the extension, the injected script relies on frames sending notifications when they have loaded/unloaded their contents; the tab will not execute a command until the current frame is fully loaded.<br>
<br>
<h2>Alert Handling</h2>

The SafariDriver is not capable of interacting with alerts like the other driver implementations (see <a href='http://code.google.com/p/selenium/issues/detail?id=3862'>issue 3862</a>). It is, however, capable of suppressing alerts so they do not cause the browser to hang.<br>
<br>
When the page script is first loaded, it overrides the native alert functions (alert, confirm, and prompt).  Each time an alert is triggered, the page script will send a synchronous message to the injected script, which forwards it to the extension (synchronously).  The extension will respond with whether there is an active WebDriver session.  If there is, the alert will be dismissed with the native function's default return value (alert: undefined, confirm: false, prompt: '').  The injected script will abort the currently executing command, if any, and return a <code>ModalDialogOpenedError</code> to the client.<br>
<br>
If there are is not an active session, the native function will be called to preserve their functionality during manual testing.<br>
<br>
<hr />

<h1>Debugging</h1>

Sometimes, a test may hang after sending a command to the SafariDriver. This usually indicates something blew up. Thankfully, the SafariDriver is quite chatty.<br>
<br>
First open the WebKit inspector on the page under test and check the console output. The console is cleared each time a page is loaded, so you'll only be able to see the logs for the most recent injected script. You can select the injected script and set break points inside the inspector, but it is sandboxed from the page, so you can't play with it using the inspector's REPL.<br>
<br>
Next, check the global page: Develop > Show Extension Builder. Select the WebDriver extension, and click "Inspect global page." Again, the SafariDriver is super chatty, so you should see what went wrong on the console.  You can set script break points for the injected page and interact with it using the REPL.<br>
<br>
<hr />

<h1>Development</h1>

Once you have manually installed the SafariDriver extension, as outlined above,<br>
you can reload your changes by re-compiling the extension, opening the extension builder, and clicking the "Reload" button for the WebDriver extension.