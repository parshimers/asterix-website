---
title: Contributing to AsterixDB
---

---

* Table of Contents
{:toc}


---

## Introduction
We warmly welcome any contributions to the AsterixDB or related (Hyracks, Pregelix) projects. A great way to start contributing is to pick a bug labelled starter in JIRA and submit a patch for it, to get acquainted with our codebase and development process.

---

## Setting up an Asterix Development environment in Eclipse

### Prerequisites
  * A suitable \*nix environment (Linux, OSX)
  * JDK 1.7+
  * A relatively recent version of Eclipse

### Steps

1. Check out Hyracks and Asterix master in two folders via git in the command line. Assume that the path is `$HOME/workspace`.

            cd $HOME/workspace/
            git clone https://github.com/apache/incubator-asterixdb/
            git clone https://github.com/apache/incubator-asterixdb-hyracks/

    You will now have `$HOME/workspace/incubator-asterixdb/` and `$HOME/workspace/incubator-asterixdb-hyracks/`.

2. Go to the hyracks and install it's artifacts to the local Maven repository by executing the following commands:


            cd $HOME/workspace/hyracks/
            mvn install -DskipTests


3. Go to the asterixdb folder and perform the same action:

             cd $HOME/workspace/asterixdb/asterix-maven-plugins/
             mvn install -DskipTests
             cd $HOME/workspace/asterixdb/
             mvn install -DskipTests

4. In Eclipse, import hyracks as an existing Maven Project.
* `File -> Import -> Maven -> Existing Maven Projects -> Next`
* Specify the Root directory as `$HOME/workspace/hyracks` and then click Next until Finish is enabled.
* If Eclipse tries to install the `m2e` (Maven To Eclipse) connector, let it do so.
* There might be some plugin errors; however, that is not a big deal. Wait until the job finishes.
* Then, click Finish.

5. Then import asterixdb as an existing Maven Project.
* `File -> Import -> Maven -> Existing Maven Projects -> Next`
* Specify root directory as `$HOME/workspace/asterixdb` and then click Next until Finish is enabled.
* Eclipse may try installing `m2e` or other plugins again; let it do so.
* Then, click Finish.
6. Fix Eclipse's build path to include compile-time generated sources.
* Right click the project where a red X mark is shown(e.g. `asterix-algebra`. Then resolve by applying the following:
  * Right click asterix-algebra. Click Build Path and Configure Build Path. Click Add Folder.
  * Under the `target -> generated sources`, check the parent folder of the `edu` folder and click OK.
  * For example, if the directory structure is "target - generated-sources - javacc - edu - uci ...", check the javacc directory and click OK. Then X mark will dissapear.
* Repeat this step to all projects which show a red X mark except "asterix-fuzzyjoin" and "asterix-transactions".
* It may be the case that only "asterix-algebra" and "asterix-runtime" will require these steps.
7. Set up Eclipse code formatting rules
* Download files [AsterixCodeFormatProfile.xml](http://wiki.asterixdb.googlecode.com/git/AsterixCodeFormatProfile.xml) [AsterixCleanupFormatProfile.xml](http://wiki.asterixdb.googlecode.com/git/AsterixCleanupFormatProfile.xml)
* Import profiles into Eclipse
  * Preferences -> Java -> Code style -> Formatter -> Import -> Select AsterixCodeFormatProfile.xml
  * Preferences -> Java -> Code style -> Clean up -> Import -> Select AsterixCleanupFormatProfile.xml
  * Preferences -> Java -> Editor -> Save actions -> Perform the selected action on save -> Format source code

---

## Signing Up for Gerrit

You should only need to perform the following steps once.

Our Gerrit server is [here](https://asterix-gerrit.ics.uci.edu/)

  1. Visit the above URL, and click on "Register" in the upper right.
  1. Gerrit uses OpenID; you may create an account using any OpenID provider, or with a Yahoo! account.
  1. Click on "Sign in with a Yahoo! ID". You will be taken to a page to log in to Yahoo!.
  1. From there, you will get a page asking you to allow asterix-gerrit.ics.uci.edu to access your email and basic information.  Approve this request.
  1. You will be directed back to Gerrit to complete the account signup. Make sure to fill out this page! It is necessary to allow you to push code changes to Gerrit for review.
  1. For "Full Name" and "Preferred Email", enter what you like. If you would like to register a different email address such as an @uci.edu address, you may do so. This is where Gerrit will send you notifications about code review requests, voting, and so on.
  1. For "Select a unique username", choose a short alphanumeric ID. This will be your ssh username for git access. Be sure to click "Select Username" to verify that the name is unique and allowed.
  1. Also provide a ssh public key here. There is information on the page on creating one if you do not have one. Click "Add" when done.
    * Note that you can create a new ssh public key just for Gerrit if you like; if you do so you will need to modify your .ssh/config file to ensure the right key is selected.
  1. Click "Continue" at the bottom of the page.
  1. Finally, verify that you have everything set up: From a command-line, type
```
ssh -p 29418 username@asterix-gerrit.ics.uci.edu
```

  1. You should see

```
  ****    Welcome to Gerrit Code Review    ****

  Hi Full Name, you have successfully connected over SSH.

  Unfortunately, interactive shells are disabled.
  To clone a hosted Git repository, use:

  git clone ssh://username@asterix-gerrit.ics.uci.edu:29418/REPOSITORY_NAME.git

```

At this point, please send an email to Chris or Ian, specifying the email address of your new account, and ask to be added to the "AsterixDB Devs" group. You may push change proposals to Gerrit, and vote +1/-1 Code Review on any proposals without being in AsterixDB Devs, but you cannot vote +2 on anything to allow a change to be merged.

---

## Configuring your local working environment for Gerrit

### One-time tasks

  1. At the command-line, run the following. Replace "username" with the user name you chose in step 7 above.
```
git config --global gerrit.url ssh://username@asterix-gerrit.ics.uci.edu:29418/
```
  1. Download my "git-gerrit" utility library:
```
git clone https://github.com/ceejatec/git-gerrit
```
  1. Ensure that the `git-gerrit/git-gerrit` script is on your PATH. You could copy it to somewhere, but it would be best to leave it inside the github clone so you can easily get updates.

### Once-per-repository tasks

  1. To work on (say) Asterix, first clone the Google Code repository (if you already have a local clone, great!).

        git clone https://github.com/apache/incubator-asterixdb

  1. `cd` into the clone repo directory, and then run the following command to create the "gerrit" remote.

        git gerrit init


---

## Making Changes - working method

  1. When you want to start working on a bug, feature, etc, first make a local `git` branch. Never work directly on `master`! `master` should always be a pure mirror of `origin/master`, ie, Google Code.


            git checkout -b my_branch


  1. Make your changes, test them, etc. Feel free to `git commit` as often as you like.
  1. **Optional**: If you like, you can push your branch up to Google Code, either to share it with others or as a backup. You may do this at whatever point in time you like.

            git push origin my_branch

  1. Every so often, you should update your local `master` mirror, and then merge that onto your working branch. This will prevent your branch from falling too far out of date, and ensure that your code review proposals will merge successfully with `master`. There are a number of ways to do this, but `git-gerrit` provides a convenience function:

            git gerrit update

  1. When you are ready to submit changes for code review, first ensure that you have committed everything locally that is necessary (`git status` should report "nothing to commit, working directory clean"). This is also a good time to update (see step 4). Then run:

            git gerrit submit

  1. This will pop open your editor to invite you to create a good commit message. This will be the single commit message which will be the only one to appear in the project's master git history. Take the time to make it clear. The editor will contain the log messages of everything you committed on your branch as a reminder, but generally you will want to delete all this and replace it with a comprehensive message. Also: As noted in the initial message, the last line of the buffer will contain a `Change-Id` field. Do not delete that line! It is used by Gerrit to identify this particular merge proposal.
  1. When you save your commit message, git-gerrit will push all of the changes from your working branch up to Gerrit. Assuming no errors, you should see output similar to the following:

            remote: Resolving deltas: 100% (1/1)
            remote: Processing changes: new: 1, refs: 1, done    
            remote: 
            remote: New Changes:
            remote:   http://fulliautomatix.ics.uci.edu:8443/30
            remote: 
            To ssh://ceej@fulliautomatix.ics.uci.edu:29418/ceej-gerrit-test
             * [new branch]      HEAD -> refs/for/master

  1. That URL under "New Changes" is your code review! Send it to others to request reviews.
  1. If you get any negative code reviews and need to make changes, you can just repeat steps 2 - 6 of the working method. Your local branch will still have all the history you put there, if you need to revert changes or look back and see what you did, etc.
  1. When you repeat step 6, you will notice two things: First, git-gerrit keeps the change message for you, including the Change-Id, so you don't have to re-invent it every time. Second, the output from `git gerrit submit` will not include the URL of the review this time. I'm not sure why; I wish it did. But if you re-visit the old URL in your browser, you should see an additional "Patch Set" containing your revised changes for people to review.

## Making More Changes

You may have as many feature branches as you want locally, and each may be at any point in the review cycle.

Once you are done with a change (that is, it has been Code Reviewed and approved in Gerrit and merged), it is probably best to start work on the next big thing on a new branch. However, git-gerrit attempts to be smart if you choose to re-use a branch. For example, it should notice if the "current" Change-Id for a branch has been merged to master, and it will create a new Change-Id and a new fresh commit message the next time you run `git gerrit submit`.

In that case, you may notice that the list of changes on the branch still contains older commits which have been merged. That is due to the unfortunate way Gerrit works; it requires squashing to a single git commit for review, which loses the association with the original commits in your history.

It is also possible that you may want to re-use a branch and git-gerrit fails to notice that the older change has been merged - or perhaps the change was Abandoned on Gerrit and not merged, and you want to continue working on the same branch to create a new proposal. In this case, run
```
git gerrit new
```
on your branch. This will force git-gerrit to create a new Change-Id and commit message the next time you run `git gerrit submit`.

---

## Using Jenkins with Gerrit

### Verification

As of right now, whenever a patch set is submitted to Gerrit (i.e. whenever a user performs 'git gerrit submit') are automatically picked up by Jenkins, which runs `mvn verify` to test the patch fully. Once the build finishes, Jenkins comments on the patchset and votes with the result (+1 for all tests passing, -1 for anything less). Ideally, and in most cases, verifocation of a patchset reqires no intervention. However there are a few exceptions to this as detailed below.

### Retrigggering builds and triggering builds manually

#### Retriggering
Occasionally, there are builds in which tests failed, but perhaps not for a reason that has anything to do with the proposed patch's changes. For example, on occasion the Integration tests can have issues with ports on the build server already being bound. The simple work around to this is to try building again.

One way to perform this is by simply visiting the link that is posted on Gerrit by Jenkins, and hitting the 'Retrigger' link on the left-hand side of the page. This will try retesting the build with the exact same parameters as last time, hopefully with a different result.

#### Manual Trigger
Builds of Gerrit patches can also be fully manually triggered from Jenkins. This can be desirable if a change is now dependent on a Hyracks change (via the Topic field feature described below), or if the patch is a draft or is not triggered automatically for any other reason. To manually trigger a build of a patchset, visit the Jenkins front-page, and click the 'Query and Trigger Gerrit patches' link. This should link to a page allowing you to manually trigger Gerrit patches. In the search field any query that can be searched in the Gerrit web interface can be used, but the default query of all open patchsets is likely fine. Hit the 'Search' button, and then click the checkbox that represents the patch that needs to be built. Then simply click 'Trigger Selected' and the patch will start building.

### Cross-project Dependencies

It is sometimes the case that a change in one related project alters interfaces or expected results that an upstream project relies on. The converse can be true as well, such as an AsterixDB change that needs a Hyracks feature. In these sorts of situations, there is a mechanism to describe to Jenkins this dependenc of one project's patchset to a particular Hyracks Change, by using the 'Topic' field in Gerrit. To utilize
this feature, perform the following steps:

  1. First submit the Hyracks change which the upstream (AsterixDB,etc..) change depends on if you have not done so already
  1. In the Hyracks change details on the Gerrit web interface, take note of the refspec in the _Download_ field of the patchset details. It will look something like `refs/changes/07/207/1`. This is what uniquely identifies the Hyracks change in Gerrit.
  1. Submit the dependent change now, if you have not done so already. In the Gerrit web interface, view the change's details. In the upper left-hand corner, there will be an empty field in a table, labeled `Topic`, with a notepad icon right-justified in the second column of the table. Click the notepad Icon, and in the resulting dialog box, enter in the refspec (`refs/changes/07/207/1`) as the Topic value.
  1. Now, manually trigger the dependent change in Jenkins (see previous section for details)

---
