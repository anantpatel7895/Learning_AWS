## VPC

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

## Subnet

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

# Route Table

Route table controls how traffic leaves or enters the subnets associated with it.

- **Default Route Table**, When you create a VPC 
    - AWS automatically creates a **main route table**.
    - Any subnet not explicitly associated with another route table uses this main route table.

- Route tables belong to a VPC.
- Route tables are linked to subnets.
- subnet can have only one route table.
- a single route table can be assiciated(linked) with multiple subnet. 
- Every route table in Amazon VPC automatically contains:
    - 10.0.0.0/16 → local

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

# Internet Gatway

1. internet gatway created as seperate component
2. after creation it is attached to the VPC
3. then it can be added to the route table.

## The Internet Gateway performs two key functions:
1. Connectivity between the VPC and the internet
2. 1:1 NAT for public IPv4 addresses

## Important Properties
- **One-to-One VPC Attachment**: You can only attach one Internet Gateway to a single VPC at a time. Conversely, one IGW cannot be shared across multiple VPCs.
- **Regional Resilience**: Although you attach it to a VPC, the IGW itself is a "horizontally scaled, redundant, and highly available" service. It doesn't live in a specific Availability Zone (AZ); it spans the entire Region. If one AZ goes down, the IGW continues to function for the other AZs in that region.
- **No Bandwidth Limits**: Unlike a NAT Gateway (which has throughput limits), an IGW scales automatically to handle any amount of traffic your instances can generate.




