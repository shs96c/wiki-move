The official documentation for Selenium can be found at http://seleniumhq.org/docs/

# Getting Involved

## Basic knowledge
Be sure to understand chapter 1 "Fundamental Concepts" of the [SVN book](http://svnbook.red-bean.com/en/1.6/svn-book.pdf).

## Subversion client

For non-developers: The recommended way is to install a GUI client.
Windows users should probably start with the free
[Tortoise SVN](http://tortoisesvn.tigris.org/).  If you are using Mac
OS X, you may wish to purchase [Versions](http://versionsapp.com/) or [SmartSVN](http://www.syntevo.com/smartsvn/index.html)

For developers: Mac OS X and many Linux distros, ship with a fine SVN
command-line client.  Linux users who don't have an `svn` command, can
install it using a package manager such as Apt or Yum.  Windows
developers have a few choices, but may wish to try
[the Slik SVN command-line client.](http://www.sliksvn.com/en/download/)
Windows users should also be aware that the Cygwin SVN client
is _not_ recommended at this time.


## Repository
Check out the website:
svn co http://selenium.googlecode.com/svn/websites/www.seleniumhq.org/


## Access
In order to be able to commit you need an Google account with the appropriate privileges - after showing a significant 'commitment' to the project you'll be granted access. In the meantime please create a new issue and attach a patch (in the form of `svn diff > myPatch.txt`) then get someone's attention to apply it by emailing the dev list or joining the IRC channel. Please be sure to sign the CLA: http://goo.gl/qC50R

## Working process
  * Communicate to the others what your plans are and what you're currently working on to be synchronized with other team members
  * Try to avoid huge reformatting/restructuring to reduce merge effort
  * Update regularly to always be on the bleeding edge and to be able to review others' changes
  * Commit only changes you're satisfied with i.e. take a look over your changes prior to submitting them to the repository

## Documentation markup language
We use reStructured Text for structuring and shaping the documentation, therefore it is required to know the basics of it by reading

  * http://jrst.labs.libre-entreprise.org/jrst/en/user/RSTpresentation.html (shorter, concise)
  * http://docutils.sourceforge.net/docs/user/rst/quickref.html (short, with lots of examples)
  * http://docutils.sourceforge.net/docs/ref/rst/restructuredtext.html (longer, more advanced)

## Local machine parsing using Sphinx
The RST files written by the team are parsed using Sphinx, a python documentation tool that takes care of the styling and superficial stuff. To do this manually, you must first install Sphinx (which also needs python)

### Install Python
Already installed on most `*`NIX, and on Mac OS X. Grab the installer from http://python.org/download/ if you use Windows.

> Notice: the version of python recommended to install is 2.5.x (latest setuptools windows installer is for version 2.5)

### Install setuptools

Setuptools is a package manager for Python. Installing setuptools will
allow you to use `easy_install` on the next step.

Get the installer from http://pypi.python.org/pypi/setuptools

### Install Sphinx
Run the following on a terminal (you may need to `sudo`):

```
    easy_install Sphinx
```

### Install Make
Depending on your OS, instructions to install will vary.

linux variants with apt-get: sudo apt-get install build-esentials

OS X - download and install the developer tools

windows: http://gnuwin32.sourceforge.net/packages/make.htm

### Generate the docs
Finally, to generate the docs. All you have to do is run this on a terminal:

```
   mvn clean jetty:run
```

That's it, you should find the site running at http://localhost:8080/

If you want to work in eclipse, prepare the project to be imported with:

```
  mvn eclipse:eclipse
```

After making changes you can see them reflected in your local server by opening a new terminal and executing:
```
  mvn clean compile
```

##### Checking for broken links

One of the available Make targets is `linkcheck` If you run `make linkcheck` from www.seleniumhq.org/src/main/rst
then Sphinx will validate all of the links in the document,
and let you know if any links are malformed, or point to dead URLs.
Note that checking all the links does take a while.

## Some rules

Here you'll find some rules we must follow to keep the documentation source files as clean as possible.

  1. Try to keep lines of no more that 80 columns, this way most text editors will be able to render the rst file in the same way, and will save us the annoyance of unending lines. The final html will be rendered as expected as the parser joins all consecutive lines to a single paragraph until the next blank line.
  1. Don't add extra spaces. The parser removes duplicated spaces anyway, but it's better to keep the source rst files as clean as possible.
  1. Don't use unicode characters, like ¶ ñ á or even  and  (double dashes). They sometimes are parsed correctly, but in some environment they don't, which brings more problems than advantages...

## Style Conventions
Most of this is simply lifted from the Python documentation.

  1. Use italics when defining a new term for the first time.
  1. Capitalize and hyphenate each of the Selenium components:  Selenium-IDE, Selenium-RC, Selenium-Grid, Selenium-Core. Additionally, "Selenium" should be spelled out in full and not abbreviated to "Sel" in the official documentation.
  1. JavaScript should be spelt with CamelCase, i.e. capital 'J' capital 'S.'
  1. URLs, file directories, file names, and selenium commands written inline should be monospaced using ````````mono````````
  1. Code Snippets should be prefaced with ".. code-block:: `<`language`>`" where `<`language`>` is the programming language
  1. Command line input and output should be a block, indented and prefaced with two colons. See the end of the Selenium-RC chapter for reference.
  1. Linux command line should be a code block prefaced with ".. code-block:: bash"

## Tips

### Text highlighting
You can apply certain style to text, just by putting some marks on it, here are some examples: `**`bold`**`, `*`italics`*`, ````````monospaced text````````.

### Links
To make links you have 4 alternatives:

  1. Easy but cluttered way: ```Text linking <http://url.linked.com>`_``
  1. Common way: ```Text linking`_`` then you can write more text and you can call the link anywhere you want like now ```Text linking`_`` Then, when you have some free space, you can write the target to that link (see the end of the file for the target linking).
  1. Titles are link targets also! All you have to do is put the title followed with an underscore like in ```Some rules`_`` or `Tips_`
  1. Links within the documentation but on other pages can be created by using the `:ref:` syntax. First, create a reference to the section you want by putting `.. _sectionName-reference:` just before the text. There are three important parts to this: The ".." at the beginning tells Sphinx not to display the output, the underscore denotes this is a reference (similar to method 3) and the colon (:) tells Sphinx that the following content is being referenced. Then, to create the link write ``:ref:`Section Title <sectionName-reference>``` "Section Title" will be displayed in the output and linked to the section named.

### Further reading
Off course, the next point to go from here if you find any problems is: http://docutils.sourceforge.net/docs/user/rst/quickref.html or http://sphinx.pocoo.org/rest.html