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
<<<<<<< HEAD
git pull
git checkout -b some_branch
git push origin some_branch
```

## View Branches

```bash
git branch -a
```

## View Graph

edit your git config file

```bash
vim ~/.gitconfig
```

```bash
[alias]
lg1 = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all
lg2 = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''          %C(white)%s%C(reset) %C(dim white)- %an%C(reset)' --all
lg = !"git lg1"
=======
git branch some_branch
git checkout some_branch
git push --set-upstream origin some_branch
```

**Note** you need to switch to the branch to start to use it, and to then push to this branch you need to set 

## Merging with Master

To merge the current branch with the master, by example:

```bash
git merge some_branch
>>>>>>> 91b62b5bb807ed8d78cd10c6a20acfd544329197
```