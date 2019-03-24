# Git Cheatsheet

Reference commands for the ones that get fogotten all too often.

## Create branch

To create a new branch, but example:

```bash
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
```