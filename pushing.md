---
title: Submitting Changes
---

* TOC
{:toc}

---

### Submitting patches in Gerrit

Once a patch set is `+2 Code Review` in Gerrit, as well as `+1 Verified`, it can be submitted by a committer.
In the simplest case, this simply means for someone who is authorized (any Apache committer) to click the 'Submit Patchset ...' button in the Gerrit web interface. However there is one case right now where care must be exercised in submitting a patch.

#### Patches which span Hyracks and AsterixDB

These patches require special care to submit. Any AsterixDB patch that requires a topic in Gerrit to verify is of this type.  In general this is the process:

1. Ensure that both patches are based on the current HEAD of master (there should be no 'Rebase Change' button). This will require reviewers to re-apply their +2, because it changes the content of the patch.
2. Submit the Hyracks patch.
3. If all went well, submit the AsterixDB patch.


__IMPORTANT__: Do *not* attempt to merge a patch that needs changes in Hyracks and AsterixDB while it needs rebasing. If the patch to Hyracks succeeds with a clean rebase, but the AsterixDB change requires a manual merge, you will leave master in a broken state!

### Pushing merged patches from Gerrit to ASF git

Once a change is submitted and merged in Gerrit, the job is only partially done. The patch is committed in Gerrit's local repository, but it is not present in the ASF git repository. This step must be done manually, preferably by the author of the patch (if they are a committer), or by one of the reviewers (if the patch is authored by a non-committer). This is simply a matter of fetching Gerrit's master branch, and then pushing those changes to the ASF git repo. The git-asf script automates and simplifies this process.

#### Prerequisites

Before we begin, some prerequisites/notes:

- You should have git-gerrit and git-asf installed (available [here](http://github.com/ceejatec/git-gerrit)) and set up properly.

- We need the SHA1 hash of the commit we want to push to ASF. This is visible in the Gerrit Web UI as a comment when a change is merged ("Change has been successfully cherry-picked as ..."). 

- You should have the credentials for pushing to the ASF repository set up in a .netrc file or stored in git. See [here](https://git-wip-us.apache.org/#committers-getting-started) for more details on setting up git with your Apache credentials.

#### Pushing the change(s)

Once the above is fulfilled, we can begin. Let `$SHA1` be the actual SHA1 hash of the commit we want to transmit from Gerrit to the ASF repo.

0. `git asf commit $SHA1`

That's all there is to it! If the patch spanned both Hyracks and AsterixDB, be sure to perform this process in both repositories.

