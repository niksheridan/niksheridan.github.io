---
layout: page
title: KB000001 Git Cheatsheet
---

Reference commands for the ones that get forgotten all too often.

## Define Identity

You must identify who you are when you commit changes.

```bash
git config --global user.name "some_user"
git config --global user.email "some_user@some_site.com"
```

## Use colours

It is simpler to identify elements when their differences are emphasised.

```bash
git config --global color.ui auto
```

## Create repo

You need a repository to store your work before you can store your work!  Most sites do this for you.

```bash
mkdir some_repo
git init
```

## Show all remote braches

Print out all branches in use:

```bash
git branch --all
```

## Added Changes Accidentally

If you've added, but not yet commited you can reverse this action:

```bash
git reset
```

## Switch to branch

```bash
git checkout some_branch
git push --set-upstream origin some_branch
```

## Create branch

To create a new branch and use it, by example:

```bash
git branch some_branch
git checkout some_branch
git push --set-upstream origin some_branch
```

**Note** you need to switch to the branch to start to use it, and to then push to this branch you need to set 

## Merging with Master (or main)

To merge the current branch with the master, by example:

```bash
git checkout main
git merge some_branch
git push --set-upstream origin main
```