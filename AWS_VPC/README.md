## 1. VPC

- VPC is a **logically isolated network**.
- A VPC belongs to a **single AWS Region**.
- In one region there can be multiple VPC.

### VPC-CIDR
**CIDR** is a method used to represent **IP address ranges**

It is written in the format:
```scss
IP_ADDRESS / PREFIX_LENGTH
```

Example:
```scss
10.0.0.0/16
```

Here:
- `10.0.0.0` → Network address  
- `/16` → Prefix length (First number of bits used for the network)
- Prefix Length decide the *network bits**, which do not change

```scss
Network bits | Host bits
192.168. | 0-255.0-255
```

---

## 2. Subnet

- Subnet is associated with **exactly one route table**.
- Subnet reside entirely within **one Availability Zone**.

### Public vs Private Subnet

#### Public Subnet

The subnet has a direct route to an **internet gateway**. Resources in a public subnet can access the public internet.

Route table:
```scss
0.0.0.0/0 → Internet Gateway
```

> In Public, Instances can access the internet as well as Internet can also access the Instance.


#### Private Subnet
The subnet does not have a direct route to an **internet gateway**. Resources in a private subnet require a NAT device to access the public internet.

Route table:
```scss
0.0.0.0/0 → NAT Gateway
```

> In private, Instances Can Access the Internet but Internet can not acess the internet.

### Subnet-CIDR

Subnet CIDR must satisfy:
```scss
Subnet CIDR ⊂ VPC CIDR
```

- CIDR blocks are defined at both VPC and subnet levels

```scss
10.0.0.0/16   (VPC CIDR)

├── 10.0.1.0/24   Public Subnet
├── 10.0.2.0/24   Private Subnet
├── 10.0.3.0/24   Database Subnet
└── 10.0.4.0/24   Cache Subnet
```

---

## 3. Route Table

Route table controls how traffic leaves or enters the subnets associated with it.

- **Default Route Table**, When you create a VPC 
    - AWS automatically creates a **main route table**.
    - Any subnet not explicitly associated with another route table uses this main route table.

- Route tables belong to a VPC.
- Route tables are linked to subnets.
- subnet can be linked only one route table.
- a single route table can be assiciated(linked) with multiple subnet. 

> Every route table in Amazon VPC automatically contains:
>  10.0.0.0/16 → local (CIDR → local)

## Route Table Important Concept
Route tables mainly decide:
```scss
Where traffic goes
```

Not:
```scss
Who can reach you
```

> Route tables mainly control where packets go when leaving a subnet, while inbound internet traffic reaches an EC2 instance through public-to-private IP mapping and internal VPC routing, with route tables only affecting the response path.

Access control is handled by:
- Security Groups
- Network ACLs

---

## 4. Internet Gatway

1. internet gatway created as seperate component
2. after creation it is attached to the VPC
3. then it can be added to the route table.

## The Internet Gateway performs two key functions:
1. Connectivity between the VPC and the internet
2. 1:1 NAT(Network Address Translation) for public IPv4 addresses

## Important Properties
- **One-to-One VPC Attachment**: You can only attach one Internet Gateway to a single VPC at a time. Conversely, one IGW cannot be shared across multiple VPCs.
- **Regional Resilience**: Although you attach it to a VPC, the IGW itself is a "horizontally scaled, redundant, and highly available" service. It doesn't live in a specific Availability Zone (AZ); it spans the entire Region. If one AZ goes down, the IGW continues to function for the other AZs in that region.
- **No Bandwidth Limits**: Unlike a NAT Gateway (which has throughput limits), an IGW scales automatically to handle any amount of traffic your instances can generate.

---

## 5. Security Group

A security group controls the traffic that is allowed to reach and leave the resources that it is associated with.

such as:
- EC2 instances
- RDS databases
- Load balancers
- ECS tasks

### IMPORTANT POINT
- It defines which **inbound** and **outbound** traffic is allowed.
- resource can have multiple security groups, and each security group can be attached to multiple resources.

### Security Group Characteristics

#### Stateful firewall

This means if **inbound traffic** is allowed, the **response traffic** is automatically allowed.

Example:
```scss
Client → EC2 (HTTP request)
EC2 → Client (HTTP response)
```
> You do not need an outbound rule for the response.

#### Allow rules only

Security groups cannot deny traffic.

They only contain allow rules.

> If traffic is not allowed → it is automatically blocked.

#### Instance-level security
Security groups protect individual AWS resources, unlike Network ACLs, which protect subnets.

### the components of inbound and outbound security group rules:

**Protocol**: The protocol to allow. The most common protocols are 6 (TCP), 17 (UDP), and 1 (ICMP).

**Port range**: For TCP, UDP, or a custom protocol, the range of ports to allow. You can specify a single port number (for example, 22), or range of port numbers (for example, 7000-8000).

**ICMP type and code**: For ICMP, the ICMP type and code. For example, use type 8 for ICMP Echo Request or type 128 for ICMPv6 Echo Request. For more information, see Rules for ping/ICMP in the Amazon EC2 User Guide.

**Source or destination**: The source (inbound rules) or destination (outbound rules) for the traffic to allow. Specify one of the following:
- A single IPv4 address. You must use the /32 prefix length. For example, 203.0.113.1/32.
- A single IPv6 address. You must use the /128 prefix length. For example, 2001:db8:1234:1a00::123/128.
- A range of IPv4 addresses, in CIDR block notation. For example, 203.0.113.0/24.
- A range of IPv6 addresses, in CIDR block notation. For example, 2001:db8:1234:1a00::/64.
- The ID of a prefix list. For example, pl-1234abc1234abc123. For more information, see Managed prefix lists.
- The ID of a security group. For example, sg-1234567890abcdef0. For more information, see Security group referencing.

---

## 6. NACLs (Network Access control list)
A network access control list (ACL) allows or denies specific inbound or outbound traffic at the subnet level.

- Each subnet in your VPC must be associated with a network ACL. If you don't explicitly associate a subnet with a network ACL, the subnet is automatically associated with the **default network ACL**.
- It controls **inbound and outbound traffic for all resources inside a subnet**.
- A **subnet** can be associated with **only one network ACL at a time**. but You can associate a network ACL with multiple subnets. 
- 

### 6.1 Where NACL Exists

Network ACL is attached to a subnet, not to individual instances.

Structure:
```plain text
VPC
 └── Subnet
      ├── Network ACL
      └── EC2 Instances
```
So:
- Security Group → Instance level
- Network ACL → Subnet level

### 6.2 Important Characteristics

#### Stateless

> Network ACL is stateless.

This means:
> if inbound request is allowed (reached), then you have to explicitly allow outbound response traffic (for the same request).

Example:
```scss
Client → Server (port 80)
Server → Client (ephemeral port)
```
> Both directions must be allowed.

### Supports Allow and Deny Rules

Unlike security groups:
```scss
Security Group → Allow only
Network ACL → Allow + Deny
```
Example:
```scss
Block malicious IP
Rule Processing Order
```
#### Rules are processed in ascending order.

Example:
```scss
Rule #	Action
100	Allow HTTP
110	Allow SSH
120	Deny All
```
> AWS checks rule 100 first, then 110, etc.

### 6.3 LIMITATION

- Network ACLs can't block DNS requests to or from the Route 53 Resolver (also known as the VPC+2 IP address or AmazonProvidedDNS). To filter DNS requests through the Route 53 Resolver, you can enable Route 53 Resolver DNS Firewall.
- Network ACLs do not filter traffic destined to and from the following:
    - Amazon Domain Name Services (DNS)
    - Amazon Dynamic Host Configuration Protocol (DHCP)
    - Amazon EC2 instance metadata
    - Amazon ECS task metadata endpoints
    - License activation for Windows instances
    - Amazon Time Sync Service
    - Reserved IP addresses used by the default VPC router
    

### 6.3 Security Group vs Network ACL

| Feature     | Security Group | Network ACL         |
| ----------- | -------------- | ------------------- |
| Level       | Instance       | Subnet              |
| Stateful    | Yes            | No                  |
| Allow rules | Yes            | Yes                 |
| Deny rules  | No             | Yes                 |
| Rule order  | Not evaluated  | Evaluated by number |

### 6.4 Inbound Rules

Inbound rules control **traffic entering the subnet**.

Example:

| Rule # | Protocol | Port | Source    | Action |
| ------ | -------- | ---- | --------- | ------ |
| 100    | TCP      | 80   | 0.0.0.0/0 | Allow  |
| 110    | TCP      | 22   | Your IP   | Allow  |
| 120    | All      | All  | 0.0.0.0/0 | Deny   |

### 6.5 Outbound Rulesf

Outbound rules control **traffic leaving the subnet**.

| Rule # | Protocol | Port | Destination | Action |
| ------ | -------- | ---- | ----------- | ------ |
| 100    | TCP      | 443  | 0.0.0.0/0   | Allow  |
| 110    | TCP      | 80   | 0.0.0.0/0   | Allow  |
| 120    | All      | All  | 0.0.0.0/0   | Deny   |

###

