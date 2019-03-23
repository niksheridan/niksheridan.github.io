# Git Cheatsheet

Reference commands for the ones that get fogotten all too often.

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

## Merging with Master

To merge the current branch with the master, by example:

```bash
git merge some_branch
```

## Merging with Master and Dealing with conflicts

### Check out, review, and merge locally

Step 1. Fetch and check out the branch for this merge request

```bash
git fetch origin
git checkout -b infraByAnsible origin/infraByAnsible
```
Step 2. Review the changes locally

```bash
git status
```

Step 3. Merge the branch and fix any conflicts that come up

```bash
git fetch origin
git checkout origin/master
git merge --no-ff infraByAnsible
```

Step 4. Push the result of the merge to GitLab
```bash
git push origin master
```

Tip: You can also checkout merge requests locally by following [these guidelines](https://gitlab.com/help/user/project/merge_requests/index.md#checkout-merge-requests-locally).