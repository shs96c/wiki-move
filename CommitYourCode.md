# Committing Code

## If You Don't Have Commit Rights

We'd still welcome your patch! There are two ways. Either use git's normal pull request or submit a pull request through the [github mirror](https://github.com/SeleniumHQ/selenium).

Before we can accept a patch, we first ask people to sign a [Contributor License Agreement (or CLA)](https://spreadsheets.google.com/spreadsheet/viewform?hl=en_US&formkey=dFFjXzBzM1VwekFlOWFWMjFFRjJMRFE6MQ#gid=0). We ask this so that we know that contributors have the right to donate the code.

## Guidelines for Committers

  * We practice trunk-based development here. All pushes are to master.
    1. This means "no remote branches"
    1. This means that I don't care how you manage things on your local repo. Go crazy. Just don't let me see.
  * The canonical repo is master on Google Code.
    1. The github mirror is a mirror and is mirrored destructively.
    1. Pushes to the github mirror will be lost
  * Your changes should represent logical pieces of work rather than the intermediate steps. This allows people following the project from the commit log to keep abreast of everything without drowning in noise.
  * We should talk about your commit message:
    * If you're fixing an issue, please refer to it by number (preceding it with a hash, eg: #23) and give a quick summary of what the problem is
    * Use your commit message to describe _why_ you've done what you've done. We can read code to figure out _what_ you've done. We can all read code. Only some of us can read minds.
    * Don't worry if it's an epic commit message: too much information is better than too little.
  * Run `./go clean test` before pushing
  * When pulling changes to your local repo, use `git pull --rebase` rather than just `git pull`.

The combination of 4 and 5 implies that before you push changes, you rebase and then squash your individual changes into a single logical change. If you're doing a large refactoring, then it's fine to use multiple logical changes. The rule of thumb: "would I commit this to the central repo if we were still using svn?"

You're welcome to do whatever you want on your local machine, so if you have something slick and shiny that makes you happy, go for it. Just don't surface oddities into the canonical repo.

A good commit message is something like: "Fixing issue #123 (on Google Code) (greedy cheese munching). Adding locking to the CheeseAdapter so that only one cheese is consumed at a time." A bad commit message looks like: "Edited CheeseAdapter.java".