# AWS Security group

A security group controls the traffic that is allowed to reach and leave the resources that it is associated with.

such as:
- EC2 instances
- RDS databases
- Load balancers
- ECS tasks

It defines which **inbound** and **outbound** traffic is allowed.

> A resource can have multiple security groups, and each security group can be attached to multiple resources.

---

# Security Group Characteristics

## Stateful firewall

This means if **inbound traffic** is allowed, the **response traffic** is automatically allowed.

Example:
```scss
Client → EC2 (HTTP request)
EC2 → Client (HTTP response)
```
You do not need an outbound rule for the response.

---

## Allow rules only

Security groups cannot deny traffic.

They only contain allow rules.

> If traffic is not allowed → it is automatically blocked.

---

## Instance-level security

Security groups protect individual AWS resources, unlike Network ACLs, which protect subnets.