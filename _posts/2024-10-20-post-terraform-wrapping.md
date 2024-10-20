---
title: "Terraform Wrapping for Scale"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - How To
  - Terraform
---
Reference syntax for terraform wrapping. See [terraform working with vars]({{ site.baseurl }}/blog/post-terraform-working-with-vars/) and
[terraform locals]({{ site.baseurl }}/blog/post-terraform-locals/) if you find gaps.

## Terraform Wrap

This is intended to be simple, and use straight forward bash scripts.

## Stash your Functions

Create a helper file, as you might want to use
this helper in another script - huge time saver for the future.

```shell
$ vim ./bash_functions/helpers.sh
```

Create a function

```bash
function fetch_tfvars() {
    vars_files=()
    for file in $(ls $1/*.tfvars); do
        prefixed_file=" -var-file=${tf_vars_dir}${file}"
        vars_files+=${prefixed_file}
    done
}
```

Write a wrapper, e.g to `tf_wrap.sh`:

```bash
#!/usr/bin/env bash
source ./bash_functions/helpers.sh
fetch_tfvars ${1}
echo executing command: terraform console ${vars_files}
terraform console ${vars_files}
```

Declare some vars in a vars file:

```hcl
variable "one" {
  default = {}
}
variable "two" {
  default = {}
}
variable "three" {
  default = {}
}
variable "four" {
  default = {}
}
variable "five" {
  default = {}
}
variable "six" {
  default = {}
}
variable "seven" {
  default = {}
}
variable "eight" {
  default = {}
}
variable "nine" {
  default = {}
}
variable "this" {
  default = {}   
}           
```

Assign values to these vars from some files in a common directory:

```shell
$ ls -l vars/dev
total 20
-rw-r--r--. 1 nsheridan nsheridan 39 Oct 20 09:30 cat.tfvars
-rw-r--r--. 1 nsheridan nsheridan 59 Oct 20 09:30 dog.tfvars
-rw-r--r--. 1 nsheridan nsheridan 21 Oct 20 09:16 primary_vars.tfvars
-rw-r--r--. 1 nsheridan nsheridan 21 Oct 20 09:16 secondary_vars.tfvars
-rw-r--r--. 1 nsheridan nsheridan 43 Oct 20 09:30 this.tfvars
$
```

So this adds in your `-var-file` parameters, so you can split your
vars files up into (more sensible) sections:

```shell
$ ./tf_wrap.sh vars/dev
executing command: terraform console -var-file=vars/dev/cat.tfvars -var-file=vars/dev/dog.tfvars -var-file=vars/dev/primary_vars.tfvars -var-file=vars/dev/secondary_vars.tfvars -var-file=vars/dev/this.tfvars
> local.those
{
  "amazing_key" = [
    "amazing_value-1",
    "amazing_value-2",
    "amazing_value-3",
    "amazing_value-4",
    "amazing_value-5",
    "amazing_value-6",
    "amazing_value-7",
    "amazing_value-8",
    "amazing_value-9",
  ]
}
>  
```
