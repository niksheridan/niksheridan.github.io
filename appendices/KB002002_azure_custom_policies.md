---
layout: page
title: KB002002 Azure Custom Policies
---

## References

Show policies

```bash
az policy definition list --query "[].[name, displayName]" -o table
az policy definition list --query "[].{Name:name, Description:displayName}" -o table | more
```

Good exmaple of a quick terraform approach can [be found here](https://techcommunity.microsoft.com/t5/azure-paas-blog/implementing-azure-policy-using-terraform/ba-p/1423775)

## Define Custom Policy

Example of [Private Link and DNS integration at scale](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/private-link-and-dns-integration-at-scale#configuration-required-by-platform-team).
Find out how to [create custom policies here](https://docs.microsoft.com/en-us/azure/governance/policy/tutorials/create-custom-policy-definition).

Hundreds of [examples can be found on github here](https://github.com/Azure/azure-quickstart-templates).

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
          "notEquals": "Deny"
        }
      ]
    },
    "then": {
      "effect": "Deny"
    }
  }
}
```

Deny creation of private endpoints

```json
{
  "description": "This policy restricts creation of private DNS zones with the `privatelink` prefix",
  "displayName": "Deny-PrivateDNSZone-PrivateLink",
  "mode": "All",
  "parameters": null,
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Network/privateDnsZones"
        },
        {
          "field": "name",
          "contains": "privatelink."
        }
      ]
    },
    "then": {
      "effect": "Deny"
    }
  }
}
```

Create the central dns entry

```json
{
  "mode": "Indexed",
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Network/privateEndpoints"
        },
        {
          "count": {
            "field": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections[*].groupIds[*]",
            "where": {
              "field": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections[*].groupIds[*]",
              "equals": "blob"
            }
          },
          "greaterOrEquals": 1
        }
      ]
    },
    "then": {
      "effect": "deployIfNotExists",
      "details": {
        "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
        "roleDefinitionIds": [
          "/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7"
        ],           
        "deployment": {
          "properties": {
            "mode": "incremental",
            "template": {
              "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "parameters": {
                "privateDnsZoneId": {
                  "type": "string"
                },
                "privateEndpointName": {
                  "type": "string"
                },
                "location": {
                  "type": "string"
                }
              },
              "resources": [
                {
                  "name": "[concat(parameters('privateEndpointName'), '/deployedByPolicy')]",
                  "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
                  "apiVersion": "2020-03-01",
                  "location": "[parameters('location')]",
                  "properties": {
                    "privateDnsZoneConfigs": [
                      {
                        "name": "storageBlob-privateDnsZone",
                        "properties": {
                          "privateDnsZoneId": "[parameters('privateDnsZoneId')]"
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "parameters": {
              "privateDnsZoneId": {
                "value": "[parameters('privateDnsZoneId')]"
              },
              "privateEndpointName": {
                "value": "[field('name')]"
              },
              "location": {
                "value": "[field('location')]"
              }
            }
          }
        }
      }
    }
  },
  "parameters": {
    "privateDnsZoneId": {
      "type": "String",
      "metadata": {
        "displayName": "privateDnsZoneId",
        "strongType": "Microsoft.Network/privateDnsZones"
      }
    }
  }
}
```

## Create

Create the [policy using az clie](https://docs.microsoft.com/en-us/azure/governance/policy/tutorials/create-and-manage#create-a-policy-definition-with-azure-cli)

```bash
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

list:

```bash
az policy definition list
```

return in the following format:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```
