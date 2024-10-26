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

## Personal Interpretation of AWX Terminology

Term                  | Familiar Term or Use
----------------------|-----------------------
Template (job)        | Job. You run a template ('Job Template')
Template (workflow)   | Workflow. (definintion required)
Project               | Combination of Template, Hosts
Hosts                 | Inventory.  Populated from inventory data read
Inventory             | Inventory sources.  All manner of sources, e.g. Netbox
Execution Environment | Runner, Build agent, worker

## Creating Custom Execution Environments ('runners')

See [this repository](https://github.com/objectivesia/awx-ee-custom/tree/main) and this
[reference post](https://thedatabaseme.de/2022/09/09/self-build-awx-execution-environment/),
to provide a repeatable, version controlled way to track
any need to stray from a vanilla path, which will be inevitable.

## Integrating with Netbox

Use AWX with netbox, this is very git centric, as I have not yet found any other sustainable
approach to working.

### Credentials

This gives you an identity for git.

* create credentials, I have SSH used keys (Credentials >> netbox_ssh_key)
* add key to repo as deployment key
* add credential as type `source control`

### Project

This gives you playbooks to choose from when you define a job template.

The below will just sync the project, not run the job, so it will call to the repository
and by doing so, attempt to authenticate.

* create project, add source control as git, associate credential
* save

### Inventory

This gives you your deployment targets, but allows you to off load the maintenance to another
team.

* create inventory, provide name
* save
* go sources > add
* choose the custom exection environment
* choose sourced from a project (choose the one you created above)
* choose project
* choose project root
* choose update on launch
* save
* sync

### Job Template

This gives you your first chance to run a playbook.

* create job template
* choose inventory you created above
* choose project you created above
* choose execution environment you created above
* choose playbook found in the project repository
* save
* launch