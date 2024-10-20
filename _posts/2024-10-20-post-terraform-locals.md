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

Create a locals file:

```shell
$ vim locals.tf
```

Add a locals block:

```hcl
locals {
  that = var.this
}
```

Load up console:

```bash
tf console -var-file=console.tfvars
```

Inspect your variable:

```shell
$ tf console -var-file=console.tfvars
> local.that
{
  "amazing_key" = "amazing value"
}
>  
```

Add in another value:

```hcl
locals {
  that = var.this
  those = { for k, v in var.this :
    k => [for i in range(1, 10, 1) : "${v}-${i}"]
  }
}
```

Inspect

```shell
$ tf console -var-file=console.tfvars
> local.those
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