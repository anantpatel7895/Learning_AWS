# Subnet

A subnet devide the a range of IP addresses in your VPC. You can create AWS resources, such as EC2 instances, in specific subnets.

# IMPORTANT
- Subnet is associated with exactly one route table.
- Each subnet must reside entirely within **one Availability Zone**.
- Each subnet must be associated with a route table, which specifies the allowed routes for outbound traffic leaving the subnet.
- 

# Public vs Private Subnet

## Public Subnet

The subnet has a direct route to an **internet gateway**. Resources in a public subnet can access the public internet.

Route table:
```scss
0.0.0.0/0 → Internet Gateway
```
> Instances can access the internet.

--- 

## Private Subnet
The subnet does not have a direct route to an **internet gateway**. Resources in a private subnet require a NAT device to access the public internet.

Route table:
```scss
0.0.0.0/0 → NAT Gateway
```
> Instances cannot receive inbound traffic from internet.

----

# IMPORTANT Concept
```code
Subnet → Route Table → Internet Gateway
```