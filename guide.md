---
layout: default
permalink: /guide/
---

# Instructor guide

### How to start

This lesson is placed after participants have created commits and branches but
before we have discussed collaboration. It can be useful to emphasize that it
can be really valuable to be able to search through the history of a project
efficiently to find when bugs were introduced or when something changed and
why.


### How to wrap up

Topics like branch design, rebasing and squashing are not covered in some
workshops due to time constraints. In this case, please point learners to the
[branch design lesson](https://coderefinery.github.io/git-branch-design/)
at the end of this lesson.


### Live better than reading the website material

It is better to demonstrate the commands live and type-along. Ideally connecting
to examples discussed earlier. Also show that `git blame` and `git show` are available
on GitHub and GitLab.


### Questions to involve participants

- Have you ever found a bug in your code and wondered whether it has affected published results?
- Have you ever wondered when, and by whom, a particular line of code was introduced?
- Have you ever found out that a code behaves differently than it used to but you are not sure when
  precisely this changed?


### Timing

This is a 45-60 minute lesson and the amount of material is typically
not too much to be able to finish in good time. There is a bonus exercise
at the end of the `git bisect` exercise which can be tackled by learners
who quickly solve the main exercise.


### Core aspects

- Key lesson is *how to find when something is broken or what commit has broken the code*


### Sessions which can be skipped if time is tight

There is usually no need to skip any parts of this lesson, but in exceptional cases
one can give the learners less time to solve the `git bisect` exercise and
solve it live in a type-along fashion.


### Typical pitfalls

#### Confusion during `git bisect` exercise

Learners may get stuck in the `git bisect` exercise if they incorrectly assign a commit
as *bad* or *good*.
To leave the bisect mode and return to the commit before `git bisect start` was issued,
one can do
```shell
$ git bisect reset
```
and start over if needed.
