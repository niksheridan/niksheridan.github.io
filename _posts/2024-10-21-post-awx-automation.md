---
title: "AWX Automation Quick Reference"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - How To
  - Kubernetes
---
Reference syntax for standing up Ansible AWX Service.

This is intended to just get something going quickly, if you are
like me who like to see a result and work backwards with purpose so
to understand all these components you may have heard about being
used in practice.

Checkout [minikube]({{ site.baseurl }}/blog/post-minikube-quick-ref/) 
for creating a local minikube cluster. Also [AWX Operator](https://ansible.readthedocs.io/projects/awx-operator/en/latest/) for installing AWX on K8s.

## AWX on Minikube

Quick start [here](https://ansible.readthedocs.io/projects/awx-operator/en/latest/installation/basic-install.html) for the original source docs.

```bash
minikube start --cpus=4 --memory=6g --addons=ingress
```

Note: this is extremely lazy way of getting the latest version, and
should not be used for production workloads:

```bash
git clone git@github.com:ansible/awx-operator.git
cd awx-operator
# (I did say it was a lazy way) get latest release
git checkout $(git tag | tail -1)
export VERSION=$(git tag | tail -1)
# install the operator
make install
```

Get pods in the awx namespace:

Create a demo manifest - note the apiVersion and type are AWX specific:

```bash
cat  << EOF > demo.yaml
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx-demo
spec:
  service_type: nodeport
EOF
```

Create a `kustomization` file (note version is embedded from the environment
variable above)

```bash
cat << EOF > kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  # Find the latest tag here: https://github.com/ansible/awx-operator/releases
  - github.com/ansible/awx-operator/config/default?ref=${VERSION}
  # this is the important bit we added above
  - demo.yaml

# Set the image tags to match the git version from above
images:
  - name: quay.io/ansible/awx-operator
    newTag: ${VERSION}

# Specify a custom namespace in which to install AWX
namespace: awx
EOF
```

Apply via kustomize:

```bash
# come back in a few minutes
k apply -k .
```

Set default namespace to awx (optional as I have set the namespace below):

```bash
kubectl config set-context --current --namespace=awx
```

Fetch secret:

```bash
kubectl get -n awx secret awx-demo-admin-password -o jsonpath="{.data.password}" | base64 --decode ; echo
```

Login with `admin` and the above secret.

```bash
# tunnel to the service
k port-forward -n awx service/awx-demo-service 1234:80
```

Then browse to `link: http://localhost:1234`

Or use the nodeport if you can get to it:

```bash
# get the node ip address 
k get nodes -o wide
# get the node port number for the service
k get service -n awx awx-demo-service 
```

Put it all together probably end up something like `link: http://192.168.39.203:30459`
