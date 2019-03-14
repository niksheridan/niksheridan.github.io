# Decisions and Supporting Reference

Thought log and decision reference repository.  

None of the infromation on these pages should be specific to a client or
particular product.  Care has been made to ensure sensitive topics are
not available in the public domain.

*If you have any concerns please make contact*.

## Architecture Design Records

Listing of latest outcomes from things discussed, and potential considerations made can be found [here](https://niksheridan.github.io/decisions)

## Documentation Framework

The purpose of this link is to focus on content, not on presentation, but not at the expense of being organised.  Markdown Architectural Decision Records (MADR) has been chosen as the view has been taken that it reduces procrastination on fonts or creation of bespoke templates.  See [ADR-0000](https://niksheridan.github.io/decisions/ADR-0000_use_of_MADRs.html) for further reference regarding jusitifcation.

* [Architectural Decision Records](https://adr.github.io/) specifically [MADR](https://github.com/adr/madr)

## Current Topics of Interest

Network automation:

* [NAPALM](https://napalm.readthedocs.io/en/latest/)
* [General Linux useage](https://niksheridan.github.io/decisions/ADR-0001_use_of_linux.html)
* Serverless (including functions and other marketing interpretations)

## Using Github Pages Platform

A reference link to github pages can be found [here](https://nicolas-van.github.io/easy-markdown-to-github-pages/), 
and using multiple SSH keys for various repositories can be found [here](https://medium.com/@dustinfarris/managing-multiple-github-deploy-keys-on-a-single-server-f81f8f23e473).

### Publishing with SSH

Generate a key

```bash
ssh-keygen -t rsa -f ~/.ssh/soapdish_repo_rsa -C "soapdish mainsite deploy key"
```

Upload the new key to github deployment keys (note the same keys cannot be used across multiple repositories)

Create (or ammend) the `~/.ssh/config` file:

```bash
# ~/.ssh/config
Host pages
HostName github.com
User Git
IdentityFile ~/.ssh/soapdish_repo_rsa
```

Add new content to the cloned repository, commit changes and push to master branch (this is a requiremet of github pages)

```bash
git add --all
git commit -m "some comment"
git push git@pages:soapdish/niksheridan.github.io
```