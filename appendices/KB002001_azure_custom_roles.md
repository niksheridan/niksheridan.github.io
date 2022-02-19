---
layout: page
title: KB002001 Azure Custom Roles
---

## References

* Creating a [custom role](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-assign-roles#create-custom-role) in Azure
* Azure [Enterprise Scale Landing Zones](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/enterprise-scale/terraform-module-caf-enterprise-scale) reference
* Azure [Terraform Module for Cloud Adoption Framework Enterprise-scale](https://registry.terraform.io/modules/Azure/caf-enterprise-scale/azurerm/latest) documentation

## Define Custom Role

Define the role in a JSON block, and exmaple is given below:

```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

## Apply Custom Roles

Apply the role using Azure CLI:

```bash
az role definition create --role-definition data_scientist_role.json
```

## Listing Custom Roles

```bash
az role definition list --subscription <sub-id> --custom-role-only true
```

## Update Custom Roles

Note there are [caveats](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-assign-roles#update-a-custom-role)

```bash
az role definition update --role-definition update_def.json --subscription <sub-id>
```

*Role updates can take 15 minutes to an hour to apply across all role assignments in that scope.*
