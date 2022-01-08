# terraform-aws-security-group
A secure, best-practice module that creates an easily configurable security group, with functionality for both cidr block and security group ingress rules.

## Instantiation
The simplest instantiation requires only an `environment`. Below is an example with all three types of dynamic rules shown.

```
module "ec2-security_group" {
  source = "git@github.com:kiawnna/terraform-aws-security-group.git"
  environment = "dev"
  
  vpc_id = "optional - required to deploy into a vpc"
  security_group_name = "optional - default is my-security-group"
  
  sg_ingress_rules = [
    {
      description = "Allow traffic from other instances protected by the same security group."
      from_port = 0
      to_port = 0
      protocol = "-1"
      security_groups = [module.ec2-security_group.security_group_id]
    }]
    
   ingress_rules = [
    {
      description = "Allow SSH traffic from my computer."
      from_port = 22
      to_port = 22
      protocol = "-1"
      cidr_block = "172.83.29.9/32"
    }]
    
  egress_rules = [
    {
      description = "Allow all outbound traffic."
      from_port = 0
      to_port = 0
      protocol = "-1"
      cidr_block = "0.0.0.0/0"
    }]
}
```

## Resources Created
* A security group

## Outputs
The security group id and name. Referenced as:

> module.sg_module_name.security_group_id
>
> module.sg_module)name.security_group_name

## Variables / Customization
Ability to define two types of ingress rules:
* **ingress_rules** - use a cidr block to determine access
* **sg_ingress_rules** - allow access from another security group
Both are lists of objects. A single object represents a single rule.

A vpc id is not required but can be provided.

See the `variables.tf` file for further customizations.

## Tags
Tags are automatically added to all resources where possible. Tags will have the following format:

```
tags = {
    Name = "shared-${var.environment}-resource"
    Deployment_Method = "terraform"
    Environment = var.environment
  }
```