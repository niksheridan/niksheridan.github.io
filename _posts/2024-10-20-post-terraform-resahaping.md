---
title: "Terraform Reshaping"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - How To
  - Terraform
---
Reference syntax for terraform reshaping of data for passing to for_each.

Most of the time, I want a list of objects, created from a convenient data shape
that I can easily use to produce resource objects at scale.

## New Map

Consider this variable:

```hcl
variable "vnets" {
  default = {}    
}                 
```

With this assignment (I've put in `resource_groups` as well):

```hcl
resource_groups = {

  rg1 = {
    name     = "rg1"
    location = "uksouth"
  }

  rg2 = {
    name     = "rg2"
    location = "uksouth"
  }

}


vnets = {

  vn1 = {
    name          = "vnet-nik-dev-uks-001"
    rg_index      = "rg1"
    address_space = ["172.16.32.0/22"]
    dns_servers = [
      "192.168.254.1",
      "192.168.254.2",
      "192.168.254.3"
    ]
    subnets = {
      rsn1 = {
        name = "RouteServerSubnet"
        # no subnet smaller than a 27 for route server subnets
        address_prefixes = ["172.16.32.0/27"]
      }
      sn1 = {
        name             = "snet-common-dev-uks-1"
        address_prefixes = ["172.16.33.0/24"]
        rt_index         = "rt1"
        nsg_index        = "nsg1"
      }
    }
    tags = {
      cost = "£0.???/hour"
    }
  }

  "vn2" = {
    "address_space" = [
      "172.17.32.0/22",
    ]
    "dns_servers" = [
      "192.168.254.1",
      "192.168.254.2",
      "192.168.254.3",
    ]
    "name"     = "vnet-nik-dev-uks-002"
    "rg_index" = "rg2"
    "subnets" = {
      "rsn1" = {
        "address_prefixes" = [
          "172.17.32.0/27",
        ]
        "name" = "RouteServerSubnet"
      }
      "sn1" = {
        "address_prefixes" = [
          "172.17.33.0/24",
        ]
        "name"      = "snet-common-dev-uks-1"
        "nsg_index" = "nsg2"
        "rt_index"  = "rt2"
      }
    }
    "tags" = {
      "cost" = "£0.???/hour"
    }
  }

}
```

This of course creates the same map:

```hcl
{ for vnet_key, vnet_value in var.vnets : vnet_key => vnet_value }
```

This, on the other hand, creates a list of objects:

```hcl
[ for vnet_key, vnet_value in var.vnets : { for subnet_key, subnet_value in vnet_value.subnets : "${vnet_key}-${subnet_key}" => subnet_value } ]
```

which produces this:

```hcl
[                                       
  {                                     
    "vn1-rsn1" = {                      
      "address_prefixes" = [            
        "172.16.32.0/27",               
      ]                                 
      "name" = "RouteServerSubnet"      
    }                                   
    "vn1-sn1" = {                       
      "address_prefixes" = [            
        "172.16.33.0/24",               
      ]                                 
      "name" = "snet-common-dev-uks-1"  
      "nsg_index" = "nsg1"              
      "rt_index" = "rt1"                
    }                                   
  },                                    
  {                                     
    "vn2-rsn1" = {                      
      "address_prefixes" = [            
        "172.17.32.0/27",               
      ]                                 
      "name" = "RouteServerSubnet"      
    }                                   
    "vn2-sn1" = {                       
      "address_prefixes" = [            
        "172.17.33.0/24",               
      ]                                 
      "name" = "snet-common-dev-uks-1"  
      "nsg_index" = "nsg2"              
      "rt_index" = "rt2"                
    }                                   
  },                                    
]                                       
```

when we pass this to for_each, it returns the following error:

```hcl
The given "for_each" argument value is unsuitable: the "for_each" argument must be a map, or set of
strings, and you have provided a value of type tuple.
```

So we have the wrong type - let's start again:

```hcl
[
  for vnet_key, vnet_value in var.vnets : {
    name          = vnet_value.name
    address_space = vnet_value.address_space
    vnet_index    = vnet_key
    rg_index      = vnet_value.rg_index
  }
]
```

produces:

```hcl
[                                          
  {                                        
    "address_space" = [                    
      "172.16.32.0/22",                    
    ]                                      
    "name" = "vnet-nik-dev-uks-001"        
    "rg_index" = "rg1"                     
    "vnet_index" = "vn1"                   
  },                                       
  {                                        
    "address_space" = [                    
      "172.17.32.0/22",                    
    ]                                      
    "name" = "vnet-nik-dev-uks-002"        
    "rg_index" = "rg2"                     
    "vnet_index" = "vn2"                   
  },                                       
]                                          
```

This can be worked on, by producing a map within the resource for example:

```hcl
resource "azurerm_virtual_network" "this" {                                                  
  for_each            = { for vnet_key, vnet_value in local.vnets : vnet_key => vnet_value } 
  ... omitted ...
}
```

So to write that as a local if we wanted to to produce the list of logical objects, and let
produce a map within the resource block shown above:

```hcl
locals {                                      
  vnets = [                                   
    for vnet_key, vnet_value in var.vnets : { 
      vnet_index    = vnet_key,               
      rg_index      = vnet_value.rg_index,    
      address_space = vnet_value.address_space
      name          = vnet_value.name         
    }                                         
  ]                                           
}                                             
```

However as we extend this, it can create a problem - lets address our subnet requirement by
creating a new locals block so we an inherit the rg_index (note also the name value):

```hcl
locals {                                                    
  vnets = [                                                 
    for vnet_key, vnet_value in var.vnets : {               
      vnet_index    = vnet_key,                             
      rg_index      = vnet_value.rg_index,                  
      address_space = vnet_value.address_space              
      name          = vnet_value.name                       
    }                                                       
  ]                                                         
  subnets = [                                               
    for vnet_key, vnet_value in var.vnets : [               
      for subnet_key, subnet_value in vnet_value.subnets : {
        vnet_index       = vnet_key,                        
        rg_index         = vnet_value.rg_index,             
        address_prefixes = subnet_value.address_prefixes    
        name             = subnet_value.name                
      }                                                     
    ]                                                       
  ]                                                         
}                                                       
```

But this produces

```hcl
> local.subnets                        
[                                      
  [                                    
    {                                  
      "address_prefixes" = [           
        "172.16.32.0/27",              
      ]                                
      "name" = "RouteServerSubnet"     
      "rg_index" = "rg1"               
      "vnet_index" = "vn1"             
    },                                 
    {                                  
      "address_prefixes" = [           
        "172.16.33.0/24",              
      ]                                
      "name" = "snet-common-dev-uks-1" 
      "rg_index" = "rg1"               
      "vnet_index" = "vn1"             
    },                                 
  ],                                   
  [                                    
    {                                  
      "address_prefixes" = [           
        "172.17.32.0/27",              
      ]                                
      "name" = "RouteServerSubnet"     
      "rg_index" = "rg2"               
      "vnet_index" = "vn2"             
    },                                 
    {                                  
      "address_prefixes" = [           
        "172.17.33.0/24",              
      ]                                
      "name" = "snet-common-dev-uks-1" 
      "rg_index" = "rg2"               
      "vnet_index" = "vn2"             
    },                                 
  ],                                   
]                                      
>                                      
```

We have ended up with a nested list, so to work around this we pass this
list of lists to the `flatten()` function:

```hcl
> local.subnets                       
[                                     
  {                                   
    "address_prefixes" = [            
      "172.16.32.0/27",               
    ]                                 
    "name" = "RouteServerSubnet"      
    "rg_index" = "rg1"                
    "vnet_index" = "vn1"              
  },                                  
  {                                   
    "address_prefixes" = [            
      "172.16.33.0/24",               
    ]                                 
    "name" = "snet-common-dev-uks-1"  
    "rg_index" = "rg1"                
    "vnet_index" = "vn1"              
  },                                  
  {                                   
    "address_prefixes" = [            
      "172.17.32.0/27",               
    ]                                 
    "name" = "RouteServerSubnet"      
    "rg_index" = "rg2"                
    "vnet_index" = "vn2"              
  },                                  
  {                                   
    "address_prefixes" = [            
      "172.17.33.0/24",               
    ]                                 
    "name" = "snet-common-dev-uks-1"  
    "rg_index" = "rg2"                
    "vnet_index" = "vn2"              
  },                                  
]                                     
>                                     
```

Subnet resource block then becomes:

```hcl
resource "azurerm_subnet" "this" {
  for_each             = { for subnet_key, subnet_value in local.subnets : 
    "${subnet_value.vnet_index}-${subnet_value.subnet_index}" => subnet_value 
  }
  ... omitted ...
}
```
