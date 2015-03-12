# Introduction

This is a work in progress.  Feel free to add things you know or remember.


## How did the Automation Atoms come about?

On 2012-04-04, jimevans asked on the #selenium IRC channel:
> "What I wanted to ask you about was the history of the automation atoms.  I seem to remember them springing fully formed, as if from the head of Zeus, and I'm sure that wasn't the case. Can you refresh my memory as to how the concept happened?"

simonstewart then proceeded to tell us a nice little story:
> Sure.  Are we sitting comfortably?  Then I'll begin.  (Brit joke, there)

> Imagine wavy lines as the screen dissolves and we're transported back to when selenium and webdriver were different projects.  Before the projects merged, there was an awful lot of congruent code in webdriver.  Congruent, but not shared.  The Firefox driver was in JS.  The IE driver was mostly C++.  The Chrome driver was mostly JS, but different JS from the Firefox driver. And HtmlUnit was unique.

> We then added Selenium Core to the mix.  Yet more JS that did basically the same thing.

> Within Google, I was becoming the TL of the browser automation team.  And was corralling a framework of our own into the mix.  Which was written in JS, and had once been based on Core before it span off on its own path.

> So: multiple codebases, lots of JS doing more or less the same thing.  And loads of bugs.  Weird mismatches of behaviour in edge-cases.

> `*shudder*`

> So I had a bit of a think. (Dangerous, I know) The idea was to extract the "best of breed" code from all three frameworks (Core, WebDriver and the Google tool).  Break them down into code that could be shared.  "The smallest, indivisible unit of browser automation" .

> Or "atoms" for short.

> These could be used as the basis the _everything_.  Consistent behaviour between browsers.  and apis.  The other important point was that the JS code in webdriver and core was grown organically.  Which is a polite way of saying "I'd rather never edit it again".  Which is a polite way of saying that it was of dubious quality .  In places.

> So: high quality was important.  And I wanted the code broken up into modules.  Because editing a 10k LOC file isn't a bright idea.

> Within Google we had a library called Closure.  Which not only allowed modularization, but "denormalization" of modules into a single file via compilation.  And I knew it was being open sourced.  So we started building the library in the google codebase.  (Where we had access to the unreleased library, code review tools and our amazing testing infrastructure).  Using Closure Library.

> "dom.js" was probably the first file I wrote.  (We can check).  Greg Dennis and Jason Leyba joined in the fun.  And the atoms have been growing ever since.

> Technically, we should be calling anything outside of "javascript/atoms" molecules.  But then we can't say that we have atomic drivers.  and use imagery from the 50s to describe them.

> `*sigh*`

jimevans replied: "molecular drivers?"

And simonstewart finished with:
> Indeed :)  The idea is that the atoms are the lowest level.  And we compose the atoms to conform to the WebDriver or RC apis in "javascript/{selenium,webdriver}-atoms" respecitively.  And then suck those in as necessary.

## A Story of Crazy-Fun

Simon Stewart :

> So, let's go back to the very beginning of the project<br>
<blockquote>When it was me, on my own<br>
(the webdriver project, that is, not selenium itself)<br>
I knew that I wanted to cover multiple different languages, and so wanted a build tool that could work with all of them<br>
That is, that didn't have a built in preference for one that made working with other languages painful<br>
ant is java biased. As is maven.<br>
nant and msbuild are .net biased<br>
rake, otoh, supports nothing very well<br>
But, and this is key, any valid rake script is also a valid ruby program<br>
It's possible to extend rake to build <i>anything</i><br>
So: rake it was<br>
The initial rake file was pretty small and manageable<br>
But as the project grew, so did the Rakefile<br>
Until there was only person who could deal with it (me), and even then it was pretty shaky<br>
So, rather than have a project that couldn't be built, I extracted some helper methods to do some of the heavy lifting<br>
Which made the Rakefile comprehensible again<br>
But they project kept. getting. bigger<br>
And the Rakefile got harder and harder to grok<br>
At the time, I was working at Google, who have a wonderful build system<br>
Google's system is declarative and works across multiple different languages consistently<br>
And, most important, it breaks up the build from a single file into little fragments<br>
I asked the OSS chaps at Google if it was okay to open source the build grammar, and they gave it the green light<br>
So we layered that build grammar into the selenium codebase<br>
With one minor change (we handle dictionary args)<br>
But that grammar sits on top of rake<br>
still, after all this time<br>
And there's a problem<br>
And that's that rake is single threaded<br>
So our builds are constrained to run serially<br>
We could use "multitask" types to improve things, but when I've tried that things got very messy, very fast<br>
So, our next hurdle is that crazyfun.rb is slow: we need to go faster<br>
Which implies a rewrite of crazyfun<br>
I'm most comfortable in java<br>
So, I've spiked a new version in java that handles the java and js compilation<br>
It's significantly faster<br>
But, and this is also important, it's a spike<br>
The code was designed to be disposable.<br>
Now that things have been proved out, I'd really like to do a clean implementation<br>
But I'm torn<br>
Do I "finish" the new, very fast crazyfun java enough to replace the ruby version?<br></blockquote>

<h2>An informal naming of our releases (by channel topic in IRC)</h2>

<ul><li>Selenium 2 beta 3 'the next generation browser release' now available - <a href='http://bit.ly/i9bkC2'>http://bit.ly/i9bkC2</a>
</li><li>Selenium 2 RC1 'the grid release' now available - <a href='http://bit.ly/jgZxW8'>http://bit.ly/jgZxW8</a>
</li><li>Selenium 2 RC2 the 'works better release' now available - <a href='http://bit.ly/mJJX1z'>http://bit.ly/mJJX1z</a>
</li><li>Selenium RC3 - "the next one is the 'big' one" release - <a href='http://bit.ly/kpiACx'>http://bit.ly/kpiACx</a>
</li><li>Selenium 2.0 Final unleashed upon the unspecting masses<br>
</li><li>Selenium 2.1.0 now available (yes, even for maven users now)<br>
</li><li>Selenium 2.2.0 now available (in nuget .. and yes, even maven)<br>
</li><li>Selenium 2.3.0 available now. A new tradition!<br>
</li><li>Selenium 2.4.0 is out -- stuff changed, but there is no blog post yet<br>
</li><li>Selenium 2.5.0. mmmm. bacon.<br>
</li><li>Selenium 2.6.0 is now available. Switch and save 15% or more on car insurance<br>
</li><li>Ruby bindings for Selenium 2.7.0 first out of the gate (on twitter at any rate). Jari is a machine...<br>
</li><li>Selenium 2.8.0 is out now -- day old bacon is still bacon</li></ul>

<ul><li>sadly we are missing IRC logs...</li></ul>

<ul><li>Selenium 2.22: The month long weekly release is finally here!<br>
</li><li>Selenium 2.23: "Now with awesome!" Wait. What? Now?!<br>
</li><li>Selenium 2.24: Now with more, erm, stuff?<br>
</li><li>Selenium 2.25: Tracking nicely<br>
</li><li>2.26 is out!<br>
</li><li>Selenium 2.27 has been released with fixes for Firefox 17. Get it while it's hot!<br>
</li><li>(there was no 2.28 topic update) code.google.com/p/selenium mirrored on github.com/seleniumHQ/selenium - we're on git now!<br>
</li><li>2.29.0 is out now! First git release with FF18 support!<br>
</li><li>BOOM! 2.31 is released with native event support for Firefox 19 even.<br>
</li><li>"correlation does not imply causation" 2.32.0 released with Firefox 20 support.<br>
</li><li>the US government is open again! Let's celebrate with 2.36 newly released, with FF24 support</li></ul>

<ul><li>2.40 is wow much automate so fixes such awe<br>
</li><li>2.41 - the last ie6 "supported" release