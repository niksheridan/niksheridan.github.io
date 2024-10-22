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