---
title: "Kubernetes Useful Reference"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - How To
  - Kubernetes
kubectl_reference: https://kubernetes.io/docs/reference/kubectl/quick-reference/
---

Useful notes for Kubernetes.

## Bash Tips

When using bash consider updating your `~/.bashrc`:

```bash
# make kubectl nicer to work with by abbreviation
alias k=kubectl
```

Bash completion - tab complete commands - increadibly useful, works with pod names as well.

```bash
# install bash completion
dnf install bash-completion
# set up autocomplete in bash into the current shell, bash-completion package should be installed first.
source <(kubectl completion bash)
# add autocomplete permanently to your bash shell.
echo "source <(kubectl completion bash)" >> ~/.bashrc 
```

Also see [kubectl Quick Reference](https://kubernetes.io/docs/reference/kubectl/quick-reference/).

## Switching between Clusters (contexts)

Get all your contexts

```bash
k config get-contexts
```

Example output:

```bash
$ k config get-contexts
CURRENT   NAME          CLUSTER       AUTHINFO    NAMESPACE
          minikube      minikube      minikube    default
*         rpk-context   rpk-cluster   rpk-admin   default
$
```

An asterisk determines the current cluster.  Now lets switch to
minikube.

```bash
k config use-context minikube
```

Example

```bash
$ k config use-context minikube
Switched to context "minikube".
$ k config get-contexts
CURRENT   NAME          CLUSTER       AUTHINFO    NAMESPACE
*         minikube      minikube      minikube    default
          rpk-context   rpk-cluster   rpk-admin   default
$
```

**caution** set-context changes values - it does switch between
them!

Checkout [minikube]({{ site.baseurl }}/blog/post-minikube-quick-ref/) for creating
a local minikube cluster, trying things out locally and switching between a scratch
environment and a your main prod environment.
