---
layout: episode
title: "Git bisect exercise"
teaching: 5
exercises: 25
questions:
  - How can we find out which commit broke or changed a functionality?
objectives:
  - Quickly find the commit that changed a behavior.
keypoints:
  - "`git bisect/grep/blame/show` is a powerful combination when doing archaeology in a project."
---

## Finding out when something broke

> *But I am sure it used to work! Strange.*

- Sometimes you realize that something broke.
- You know that it used to work.
- You do not know when it broke.
- First find out a commit in past when it worked.
- Then bisect your way until you find the commit that broke it.

```shell
$ git bisect start
$ git bisect good 89578ed  # this is a commit that worked
$ git bisect bad HEAD      # last commit is broken
  # now compile and/or run
  # after that decide whether
$ git bisect good
  # or
$ git bisect bad
  # now compile and/or run
  # after that decide whether
$ git bisect good
  # or
$ git bisect bad
  # iterate until commit is found
```

- This can even be automatized with `git bisect run <script>`.
- For this you write a script that returns zero/non-zero (success/failure).

---

## Git bisect exercise

Clone or fork it from [here](https://github.com/bast/git-bisect-exercise).


### Motivation

The motivation for this exercise is to be able to do archaeology with Git on a
source code where the bug is difficult to see visually. **Finding the offending
commit is often more than half the debugging**.


### Background

The script `get_pi.py` approximates pi using terms of the Nilakantha series. It
should produce 3.14 but it does not. The script broke at some point and
produces 3.57 using the last commit:

```
$ python get_pi.py

3.57
```

At some point within the 500 first commits, an error was introduced. The only
thing we know is that the first commit worked correctly.


### Your task

Use `git bisect` to find the commit which broke the computation.


### How to find the first commit

```
$ git log --oneline --reverse | head
```


### Bonus exercise

Write a script that checks for a correct result and use `git bisect run` to
find the offending commit automatically.
