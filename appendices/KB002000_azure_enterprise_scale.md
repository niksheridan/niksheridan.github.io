---
layout: page
title: KB002000 Azure Enterprise Scale
---

## References

* Azure [Enterprise Scale Archetype Definition](https://github.com/Azure/terraform-azurerm-caf-enterprise-scale/wiki/%5BUser-Guide%5D-Archetype-Definitions)

## Archetype Definitions

Note this supoprts the [templatefile()](https://www.terraform.io/language/functions/templatefile) function in terraform.

This is fully supported in yaml.

## Role Creation: Important

Note the point regarding the ```name``` field in custom
role definitions:

> To keep the archetype_definition template as lean as
 possible, we simply declare the value of the name
 field from the resource templates (by type). The
 exception is Role Definitions which must have a GUID
 for the name field, so we use the roleName value from
 properties instead.
