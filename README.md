# Cloud Map Namespace Terraform Module

## Overview
This Terraform module creates an **AWS Cloud Map Private DNS Namespace**, optionally associates it with a Route 53 Resolver Profile, and publishes namespace metadata to AWS SSM Parameter Store. It is designed to be used within your **Account Factory for Terraform (AFT) pipeline**, triggered via `account-request` and executed through `account-customization`.

---

## Features
1 Creates AWS Cloud Map Private DNS Namespace for service discovery and R53 requirements.  
2 Associates the namespace with a Route 53 Resolver Profile (optional).  
3 Publishes `namespace_id`, `namespace_name`, and `hosted_zone_id` to SSM Parameter Store for automation consumption.  
4 Supports dynamic, environment-consistent namespace naming using environment, prefix, region, and account suffixes.

---

## Repository Structure
```
/modules/cloud-namespace       # Module definition (this repo)
/account-customization         # Calls this module, passing required variables
/account-request               # Triggers customization via AFT
```

---

## Usage
In your **`account-customization`**, invoke the module as:

```hcl
module "cloudmap_namespace" {
  source = "git@gitprod.bofa.com:AFP/afp_terraform-aws-cloudmap-ns.git?ref=feature/cloudmap-ns"

  cloudmap_namespace_name = local.namespace_name_1
  vpc_id                  = module.region1_vpc.vpc_outputs.vpc_attributes.id
  vpc_name_prefix         = local.vpc_name_prefix_1

  providers = {
    aws.target = aws.region1
  }
}
```

---

## Input Variables
| Name | Description | Type | Required |
|------|-------------|------|----------|
| `cloudmap_namespace_name` | Name of the Cloud Map Private DNS Namespace to create. | `string` | ✅ |
| `vpc_id` | VPC ID to associate with the namespace. | `string` | ✅ |
| `vpc_name_prefix` | Prefix for naming Route 53 association resources. | `string` | ✅ |

---

## Outputs
The module writes metadata to AWS SSM Parameter Store at:
/aft/cloudmap_namespace_info

as a JSON string containing:
- `namespace_id`
- `namespace_name`
- `hosted_zone_id`

Optionally, you may add outputs in your environment if needed:

```hcl
output "namespace_id" {
  value = aws_service_discovery_private_dns_namespace.cloudmap_ns.id
}
output "namespace_name" {
  value = aws_service_discovery_private_dns_namespace.cloudmap_ns.name
}
output "hosted_zone_id" {
  value = aws_service_discovery_private_dns_namespace.cloudmap_ns.hosted_zone
}
```

---

## Prerequisites
1 Terraform >= 1.9.0  
2 AWS Provider >= 5.0.0, < 6.0.0  
3 IAM permissions to create Cloud Map namespaces, associate with Route 53 Resolver Profiles, and write to SSM Parameter Store.  
4 AWS Account Factory for Terraform (AFT) pipeline setup for execution.

---

## License
Private - Internal Use Only

---

## Contact
For questions or issues, please reach out to your **AFP/AFT platform engineering team.**

---
