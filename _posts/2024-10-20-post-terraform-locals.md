---
title: "Terraform Locals Loop Reference"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - How To
---
Reference syntax for terraform loops.

## Terraform Console

Don't forget terraform console, and also how you can load vars in
for experimenting.

Create a variable declaration file:

```shell
$ vim vars.tf
```

Declare you variable:

```hcl
variable "this" {}
```

Assign values to your varible:

```shell
$ vim console.tfvars
```

Add contents to the file

```hcl
this = {
  amazing_key = "amazing value"
}
```

Load up console:

```bash
tf console -var-file=console.tfvars
```

Inspect your variable:

```shell
$ tf console -var-file=console.tfvars
> var.this
{
  "amazing_key" = "amazing value"
}
> var.this.amazing_key
"amazing value"
>  
> { for k, v in var.this : k => [ for i in range(1,10,1) : "${v}-${i}" ] }
{
  "amazing_key" = [
    "amazing value-1",
    "amazing value-2",
    "amazing value-3",
    "amazing value-4",
    "amazing value-5",
    "amazing value-6",
    "amazing value-7",
    "amazing value-8",
    "amazing value-9",
  ]
}
>  
```
