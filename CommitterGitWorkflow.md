#Workflow for selenium committers

# Introduction

This page describes a standard committer workflow when fetching stuff from github into google code.

The state-machine of day-to-day git use looks like this:

![http://wiki.selenium.googlecode.com/git/img/git-rebase-state-machine.png](http://wiki.selenium.googlecode.com/git/img/git-rebase-state-machine.png)

If you loose your confidence anywhere in steps on the right hand side,run  git rebase --abort to restore initial state


# Handling a pull request

We do trunk-based development, and like to only commit logical changes rather than interim steps. Consequently, the way that SimonStewart handles pull requests follows. This assumes that you've received the initial PR email, which gives the URL of the remote repo to pull from.

First, create a new remote for the repo to pull from and prepare a new branch to do the feature work on:

```
git remote add somename https://github.com/<username>/selenium
git fetch somename
git checkout master
git pull
git checkout -b feature
```

Now, identify the revisions to pull. GitHub does a decent job of showing the revision numbers in the URLs of the revisions in the PR. Cherrypick each of those into your branch, adding the sign off each time:

```
git cherry-pick -s abc1234efg
```

Repeat as necessary. This means that the head of your branch contains each of the changes from the pull request. We like to commit features rather than minor steps, so now we squash the commits together. Let's imagine that there were three changes:

```
git rebase -i HEAD~3
```

Pick the first one, then "fixup" the other changes in this diff. You'll end up with a single commit. At this point, you may chose to amend the commit message, but leave the "signed off" section in place! When you've tested the change and are happy with it:

```
git checkout master
git pull
git merge feature
git push
git branch -D feature
```

This will result in a single commit on to HEAD that contains all the changes from the pull request, though it may well appear to come from the past. Which isn't confusing at all.

KristianRosenvold has a different process:

```
git pull --no-ff https://github.com/<username>/selenium master
```

The exact url will be noted in the pull request mail from github. Please note the branch name "master" may be different.

This pull mechanism will create one commit for the actual submission and a merge on top of that, which will identify the person who accepted the pull request.

# Squashing and Rebasing

We prefer that you squash and rebase whatever it is you are submitting
to the project, so that each commit consists of a logical change that
compiles and tests well.

This also helps keep a clean one-dimensional history of changes that
are fairly easy to revert when a patch needs to be backed out.
Patches integrated by merging in git have a tendency to be hard to
re-revert (applying the revert) again.  When accepting other people's
changes, warn them that their submission will be rebased.

Squashing a patch can be done interactively:

```
git rebase -i --autosquash `git merge-base HEAD origin/master`
```

This will automatically squash any fixups and drop you into your
editor to pick which commits should be treated as fixups, or which
should remain as distinct, individual commits.

After squashing the branch you can rebase it on master by doing this:

```
git rebase origin/master
```