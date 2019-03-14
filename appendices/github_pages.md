
# Using Github Pages Platform

A reference link to github pages can be found [here](https://nicolas-van.github.io/easy-markdown-to-github-pages/), 
and using multiple SSH keys for various repositories can be found [here](https://medium.com/@dustinfarris/managing-multiple-github-deploy-keys-on-a-single-server-f81f8f23e473).

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