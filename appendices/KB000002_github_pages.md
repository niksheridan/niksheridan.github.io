---
layout: page
title: KB000002 Using Github Pages Platform
---

A reference link to github pages can be found [here](https://github.com/nicolas-van/bootstrap-4-github-pages),

## Publishing with SSH

Generate a key

```bash
ssh-keygen -t rsa -f ~/.ssh/niksheridan_repo_rsa -C "Nik Sheridan mainsite deploy key"
```

Upload the new key to github deployment keys (note the same keys cannot be used across multiple repositories)

Create (or ammend) the `~/.ssh/config` file:

```bash
# ~/.ssh/config
Host pages
HostName github.com
User Git
IdentityFile ~/.ssh/niksheridan_repo_rsa
```

Add new content to the cloned repository, commit changes and push to master branch (this is a requiremet of github pages)

```bash
git add --all
git commit -m "some comment"
git push git@pages:niksheridan/niksheridan.github.io
```