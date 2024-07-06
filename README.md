# Git Bisect Demo

## Setting up the environment

First we add some commits to our file for demonstration purpose. And add sum sort of unexpected behavior or bad commit(s).

## Procedure

First we need to determine the good and bad stages to setup the `git bisect`.
For this we need to check the log. We check the log using `git log`.
It should show something like the following:

    commit 2c8652372816007b18efbbfe7ee3ae3c3d7f33ff (HEAD -> main)
    Author: **
    Date:   Tue Jul 2 22:17:32 2024 +0600

        Add word 'stream'.

    commit 7e4c4ece4e3212de38533495307bfca46803a163
    Author: **
    Date:   Tue Jul 2 22:17:12 2024 +0600

        Add word 'the'.

    commit 9234b5f364f6c9f5f095b80e98989d1769c07c5c
    Author: **
    Date:   Tue Jul 2 22:16:55 2024 +0600

        Change 'boat' to 'car'.

    commit f2cb00b36bf4404f57deb2434fd2f32115ebd0d8
    Author: **
    Date:   Tue Jul 2 22:15:53 2024 +0600

        Add word 'down'.

    commit 151c50a5956bcb353c3244fcfc33731e616a2fb4
    Author: **
    Date:   Tue Jul 2 21:58:34 2024 +0600

        Add word 'gently'.

    commit e4960c6e81705506dd1849272a01fc23b2fd717e
    Author: **
    Date:   Tue Jul 2 21:58:14 2024 +0600

        Add word 'boat'.

    commit c633e046cae68e1fc72a79a66e7b858c845ea18c
    Author: **
    Date:   Tue Jul 2 21:57:46 2024 +0600

        Add word 'your'.

    commit b377c80d039bbd22e9896bb02039a9eea2a77314
    Author: **
    Date:   Sun Jun 30 01:41:49 2024 +0600

        Add third row.

    commit d7bbf184cd6edb284a9a88510e3ccb59bfae8776
    Author: **
    Date:   Sun Jun 30 01:38:32 2024 +0600

        Add second row.

    commit f86e68e11dd337378f1b0d72bbcbf5006fc9be1a
    Author: **
    Date:   Sun Jun 30 01:34:28 2024 +0600

        Add first row.

Let's take the `commit e4960c6e81705506dd1849272a01fc23b2fd717e` as our good/start of the bisect and the last `commit 2c8652372816007b18efbbfe7ee3ae3c3d7f33ff` as our bad/end commit.

We start the bisect process using the command `git bisect start`.

Add the good/start of the bisect using `git bisect good e4960c6e81705506dd1849272a01fc23b2fd717e`,
Add the bad/end of the bisect using `git bisect bad 2c8652372816007b18efbbfe7ee3ae3c3d7f33ff`.

This should start the bisect process and checkout a commit between these points.
As an example suppose we have gotten the following:

    Bisecting: 2 revisions left to test after this (roughly 1 step)
    [f2cb00b36bf4404f57deb2434fd2f32115ebd0d8] Add word 'down'.

the text file contains:

    row
    row
    row
    your
    boat
    gently
    down

it's a good commit as it still contains the word 'boat' which is the expected behavior. So we continue the process by putting the command `git bisect good`.
This checks out to the commit:

    Bisecting: 0 revisions left to test after this (roughly 1 step)
    [7e4c4ece4e3212de38533495307bfca46803a163] Add word 'the'.

the text file now contains:

    row
    row
    row
    your
    car
    gently
    down
    the

Here the word 'car' is present which is not our expected behavior, so we continue the process by putting the command `git bisect bad`. And it checks out to the following commit:

    Bisecting: 0 revisions left to test after this (roughly 0 steps)
    [9234b5f364f6c9f5f095b80e98989d1769c07c5c] Change 'boat' to 'car'.

Where the content of the file is:

    row
    row
    row
    your
    car
    gently
    down

As it's also a bad commit we use the command `git bisect bad` and the process should return the commit where the bug is produced. And it is:

    9234b5f364f6c9f5f095b80e98989d1769c07c5c is the first bad commit
    commit 9234b5f364f6c9f5f095b80e98989d1769c07c5c
    Author: **
    Date:   Tue Jul 2 22:16:55 2024 +0600

        Change 'boat' to 'car'.

    bisect-test.txt | 4 ++--
    1 file changed, 2 insertions(+), 2 deletions(-)

We have found out first bad commit. To end the bisect process we use the command `git bisect reset`.
