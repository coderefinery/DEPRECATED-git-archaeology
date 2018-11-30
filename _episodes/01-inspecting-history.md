---
layout: episode
title: "Inspecting history"
teaching: 15
exercises: 0
questions:
  - How can we find out who introduced a line of code and when exactly?
objectives:
  - Quickly find a line of code, find out who introduced it, when, and why.
keypoints:
  - "`git show` shows details of a commit"
  - "`git grep` is a fast way to find specific patterns"
  - "`git blame` shows commit hashes and authors for each line of a file"
  - "`git log --grep` finds patterns in commit messages"
  - "`git log -S` shows when code was added and removed"
  - "`git checkout -b <name> <hash>` is the recommended mechanism to inspect old code"
---

## Inspecting commits

At any moment we can inspect individual commits with `git show`:

```shell
$ git show e83c51633

commit e83c5163316f89bfbde7d9ab23ca2e25604af290
Author: Linus Torvalds <torvalds@ppc970.osdl.org>
Date:   Thu Apr 7 15:13:13 2005 -0700

    Initial revision of "git", the information manager from hell

...
diff --git a/README b/README
new file mode 100644
index 000000000..27577f768
--- /dev/null
+++ b/README
@@ -0,0 +1,168 @@
+
+       GIT - the stupid content tracker
+
+"git" can mean anything, depending on your mood.
+
+ - random three-letter combination that is pronounceable, and not
+   actually used by any common UNIX command.  The fact that it is a
+   mispronounciation of "get" may or may not be relevant.
+ - stupid. contemptible and despicable. simple. Take your pick from the
+   dictionary of slang.
+ - "global information tracker": you're in a good mood, and it actually
+   works for you. Angels sing, and a light suddenly fills the room.
+ - "goddamn idiotic truckload of sh*t": when it breaks
+
+This is a stupid (but extremely fast) directory content manager.  It
+doesn't do a whole lot, but what it _does_ do is track directory
+contents efficiently.
...
```

We see that the start of the hash is enough if it is unique.

Compare this with: [https://github.com/git/git/commit/e83c51633](https://github.com/git/git/commit/e83c51633)

---

## Grepping code

What if the combination of `git log` and `git show` is not enough?

```shell
$ git grep -i fixme

densfit/df_vxc.F:!fixme call XC integrator
dirac/dirgrd.F:!FIXME: there is some issue with one-step - investigate later
dirac/dirrdn.F:!       fixme num grid report should be independent of DFT
dirac/dirscf.F:        !fixme: michal for InteRest
dirac/dirscf.F:          !fixme: michal for InteRest
dirac/dirscf.F:          !fixme: michal for InteRest
embedding/get_vemb_mat.F:!fixme irep can be < 0 in NONREL runs
eri/eri2out.F:!fixme
eri/eri2out.F:!fixme
eri/eri2out.F:!fixme
gp/gpkrmc.F:C     FIXME: dette ser ikke ud til at virke for complex groups.
interest/module_interest_hrr.f90:  !-- fixme --!
interest/module_interest_interface.F90:    !> fixme: contracted basis sets
```

- Greps entire repository below current directory.
- Super fast. Sometimes it is worth creating a Git repository just for one `git grep`.

---

## Finding out who introduced a modification and when

- `git blame` is a fun and useful command to find out when a specific line got introduced and by whom.
- This is important to judge the impact of a bug:
  - When was it introduced?
  - For how long has this bug been around?
  - Has this bug been released?
  - Has this buggy code been used by others?
  - Who introduced it? Not to blame people but to possibly get more information.

```shell
$ git blame <filename>
```

Example from real life:

```
faa4617a (Radovan Bast      2012-07-02 11:04:45 +0200) ! get the one electron Hamiltonian
e564cfe8 (A. J. Thorvaldsen 2012-12-25 00:59:21 +0100) H1 = 0*S
e564cfe8 (A. J. Thorvaldsen 2012-12-25 00:59:21 +0100) call mat_ensure_alloc(H1, only_alloc=.true.)
faa4617a (Radovan Bast      2012-07-02 11:04:45 +0200) call interface_scf_get_h1(H1)
faa4617a (Radovan Bast      2012-07-02 11:04:45 +0200)
e6cfa2cf (Radovan Bast      2012-03-01 10:06:39 +0100) ! get the two electron contribution (G) to Fock matrix
e564cfe8 (A. J. Thorvaldsen 2012-12-25 00:59:21 +0100) G = 0*S
e564cfe8 (A. J. Thorvaldsen 2012-12-25 00:59:21 +0100) call mat_ensure_alloc(G, only_alloc=.true.)
810e14d8 (Radovan Bast      2012-07-01 17:19:56 +0200) call interface_scf_get_g(D, G)
761d5c27 (Radovan Bast      2012-05-18 16:28:34 +0200)
e6cfa2cf (Radovan Bast      2012-03-01 10:06:39 +0100) ! Fock matrix F = H1 + G
761d5c27 (Radovan Bast      2012-05-18 16:28:34 +0200) F = H1 + G
```

Rather typical timeline:

> *"Who the %&!@!!! wrote this crap?!? Oh, it was me."*

Who was the last to edit a specific line of the source file for `git grep` and when and why?

- [https://github.com/git/git/blame/master/grep.c](https://github.com/git/git/blame/master/grep.c)

---

## Grepping commit messages

- Another possible scenario is that you're looking for a particular commit, but can't
easily find it with `git blame`.
- You can however remember (or guess) some keywords from the commit message
- Commit messages can also be grepped!

Real-life example:
```shell
$ git log --oneline --grep "removed"

37f3a26 fixed a litle error I just introduced in the makefile, and removed TRASHUP
b9a3382 checked the addDfileds and removed the commented stuff
0ecc02a removed unused multipole-Ewald files
```

---

## Finding removed code

The day will come when you are in this situation:

> *I remember there used to be a line containing the word "great idea".
> Now it is gone:*

```shell
$ git grep 'great idea'

[no output]
```

Sometimes also the log does not help because the commit messages are not helpful:

```shell
$ git log --oneline

2bc6228 even more ideas
bad55db more ideas
81191b5 this is not a useful commit message
baae463 initial layout
```

What now?

We can figure out when it disappeared:

```shell
$ git log -S 'great idea'

commit 81191b5407687745e7f36b8ae4d78b7ea2377ff0
Author: Radovan Bast <bast@users.noreply.github.com>
Date:   Tue Dec 13 22:27:06 2016 +0200

    this is not a useful commit message

commit baae463ee30ff07a2e346852817cbd2038bd05df
Author: Radovan Bast <bast@users.noreply.github.com>
Date:   Tue Dec 13 22:26:48 2016 +0200

    initial layout
```

Now let us have a look at that commit:

```shell
$ git show 81191b5

commit 81191b5407687745e7f36b8ae4d78b7ea2377ff0
Author: Radovan Bast <bast@users.noreply.github.com>
Date:   Tue Dec 13 22:27:06 2016 +0200

    this is not a useful commit message

diff --git a/ideas.txt b/ideas.txt
index a09af89..a657b2b 100644
--- a/ideas.txt
+++ b/ideas.txt
@@ -1,3 +1,2 @@
-great idea
 bad idea
 mediocre idea
```

Indeed! Thank you, Git!

---

## Finding a developer to talk to (large projects)

Example: you want to know who implemented a specific keyword/functionality.

- `git grep` for the keyword or screen message.
- Then with `git blame` find the person/commit that introduced it.
- With `git show` verify the commit that introduced the change.

---

## Going back in time

We do not have to branch from the tip of the branch (HEAD).
We can branch from arbitrary (earlier) hash:

```shell
$ git checkout -b <name> <hash>
```

This is the recommended mechanism to inspect old code (example: hash *abc123*):

```shell
$ git checkout -b museum abc123  # create branch called "museum" from hash abc123
  # do some archaeology ...
  # "What was I thinking back then!?"
  # "Aha! Hmm."
$ git checkout master            # after you are done switch back to "master"
$ git branch -d museum
```

Branches help us to keep the repository organized.

---

## Finding commits not merged upstream

So you would like to know which commits from current branch have not been
merged to `<branch>`. No problem with `git cherry`:

Here is an example:

```shell
$ git clone https://github.com/bast/git-rebase-squash-exercise.git
$ cd git-rebase-squash-exercise
$ git checkout haiku
$ git cherry master

+ 7e1f903f77d2b6d8ac2b9a403e30367b5b7eeb4a
+ 3ff39a18a24074e83ee30e4e959d3863ae05ed82
+ 54fba2116e74af3170314d0e2be85b34c6f27490
+ a3278e36ddbbd8b1feff38ba973ede4b3d9323c9
+ 47a007d563adb03aeedbddeb483d3c6c0301c10a
+ 65870f9c4147bace639a92b08250911b541364fe
```

These six commits are on the branch `haiku` but are not on `master`.

Sometimes branches received different commits, then you will see hashes with
"+" and "-" in front of them.
