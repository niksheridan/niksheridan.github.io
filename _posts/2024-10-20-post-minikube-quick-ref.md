---
title: "Minikube Quick Reference"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - How To
  - Kubernetes
---
Reference syntax for standing up minikube.

## Minikube on KVM2

Install on fedora:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

Start 3 node cluster

```bash
minikube start --nodes 3
```

Destroy cluster

```bash
minikube delete
```

Checkout [kubernetes cheats]({{ site.baseurl }}/blog/post-kubernetes-cheats/) for using
helpful commands to get things deployed.