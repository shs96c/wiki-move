# Introduction

The python bindings for Selenium 2 are now available. The bindings include the full functionality of Selenium 1 and 2 (WebDriver). The package currently supports the Remote, Firefox, Chrome and IE protocols natively.

_note_ : Currently Selenium only supports Python 2.6, 2.7, 3.2, 3.3


If using selenium 1, before attempting to run a test, be sure to [download the Selenium Server jar](http://code.google.com/p/selenium/downloads/list) file, and run it via
```
java -jar selenium-server-standalone.jar
```
in your terminal/cmd prompt, before attempting to run a test. Selenium 2 however, does not require the jar file.

# Installation

Currently, there are two versions of Selenium available for use.

## Latest Official Release
The first version, is the latest official release, which is available on the Python Package Index http://python.org/pypi/selenium. To use this version, in your terminal type:

`[sudo] easy_install selenium` or `[sudo] pip install selenium`


## Development Version

The second version, is the current code from trunk. To use this, checkout the trunk repository at http://code.google.com/p/selenium/source/checkout.
After the download is completed, cd to the root of the downloaded directory via terminal/cmd prompt, then cd to the `py` folder. Perform the following command:

`(sudo) ./go py_install`.

Upon completion, the package should be installed successfully.

One advantage of using trunk as of writing, is the reorganization of the package. Previously, to initialize a browser you had to perform,

```
from selenium.firefox.webdriver import WebDriver

driver = WebDriver()
```

This has been changed, so now all that is required is:

```
from selenium import webdriver

driver = webdriver.Firefox()

```


## Testing Source

When developing Selenium, it is recommended you run the tests before and after making any changes to the code base. To perform these tests, run
```
./go //py:firefox_test:run
```
from the root of your local copy. Other browsers can be tested by changing `firefox` to the browser you wish to test, for example:
```
./go //py:chrome_test:run
./go //py:phantomjs_test:run
```
To rerun a single test method that may have failed, you can do that like this:
```
./go //py:firefox_test:run method=testShouldExplicitlyWaitForASingleElement
```
To run as a different version of python (for example if your default is 2.7 and you have 3 installed)
```
./go test_py pyversion=python3
```

# Usage

Depending on the driver you wish to utilize, importing the module is performed by entering the following in your python shell:

Selenium 1:
`from selenium import selenium`


Selenium 2:

Using the latest official release:
```
from selenium import webdriver
```

## API Documentation / Viewing Available Functionality

To read the API documentation of the Python Bindings go to the [Python bindings API doc page](http://selenium.googlecode.com/svn/trunk/docs/api/py/index.html).

Alternatively use your python shell to view all commands available to you, after importing perform:

Selenium 1:
```
dir(selenium)
```

Selenium 2:
```
dir(webdriver)
```

To view the docstrings (documentation text attached to a function or method), perform
```
print 'functionname'.__doc__
```

where functionname is the function you wish to view more information on. For example,

```
print selenium.open.__doc__
```

## Comparison with Java Bindings

Here is a summary of the major differences between the python and Java bindings.

### Function Names

Function names separate compound terms with underscores, rather than using Java's camelCase formatting. For example, in python
```
title
```
is the equivalent of
```
getTitle()
```
in Java.

### Flatter Structures

To reflect pythonic behavior of flat object hierarchies the python bindings e.g.
```
find_element_by_xpath("//h1")
```
rather than
```
findElement(By.xpath("//h1"));
```

but it does give you the freedom of doing find\_element(by=By.XPATH, value='//h1')

# Browser Support

All of the browsers supported by the Java implementation of Selenium are available in the Python bindings. For example:

### Selenium 1 - Internet Explorer
```
from selenium import selenium

selenium = selenium("localhost", 4444, "*iexplore", "http://google.com/")
selenium.start()
```

### Selenium 1 - Firefox
```
from selenium import selenium

selenium = selenium("localhost", 4444, "*firefox", "http://google.com/")
selenium.start()
```

### Selenium 2 - Firefox
Latest Official Release:
```
from selenium import webdriver

driver = webdriver.Firefox()
```

### Selenium 2 - Chrome
Latest Official Release:
```
from selenium import webdriver

driver = webdriver.Chrome()
```
### Selenium 2 - Remote
Latest Official Release:
```
from selenium import webdriver

driver = webdriver.Remote( browser_name="firefox", platform="any")
```
### Selenium 2 - IE
Latest Official Release:
```
from selenium import webdriver

driver = webdriver.Ie()
```