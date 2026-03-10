# AWS VPC Packet Processing Order (Internet → EC2)

Understanding the **exact order AWS processes a packet** is extremely helpful for debugging VPC networking.  
Below is the decision chain used in **Amazon VPC** when traffic comes from the internet to an **EC2 instance**.

---

# 1. High-Level Packet Path (Internet → EC2)
```plain text
Client (Laptop)
   ↓
Internet
   ↓
AWS Edge Network
   ↓
Internet Gateway
   ↓
Public IP → Private IP mapping
   ↓
VPC Router (route table lookup)
   ↓
Subnet
   ↓
Network ACL
   ↓
Security Group
   ↓
Elastic Network Interface (ENI)
   ↓
Instance OS
   ↓
Application (SSH, Nginx, etc.)
```



Component to know here:

- Internet Gateway

---

# 2. Step-by-Step AWS Decision Tree

## Step 1 — Internet Gateway receives the packet

Example:
```plain text
ssh 3.110.40.5
```

AWS receives traffic destined for that **public IP**.

The gateway checks whether that public IP belongs to an EC2 instance.

Then it performs NAT(Network Address Translation) mapping:
```plain text
Public IP → Private IP
3.110.40.5 → 10.0.1.10
```

If no mapping exists → **packet dropped**.

---

## Step 2 — VPC Router

Now the packet destination is:
```plain text
10.0.1.10
```

The VPC router checks the route table.

Every route table contains:
```scss
10.0.0.0/16 → local
```

This means:

> Deliver the packet inside the VPC network.

The router determines which subnet contains the IP.

Example:
```scss
10.0.1.10 ∈ 10.0.1.0/24
```

Packet forwarded to that subnet.

---

## Step 3 — Network ACL (Subnet Firewall)

Next AWS evaluates the **Network ACL** attached to the subnet.

Example rule:
```scss
Inbound
Allow TCP 22
Source: your IP
```

Important characteristics:

- Stateless
- Evaluated **before security groups**

If denied here → **packet dropped**.

---

## Step 4 — Security Group (Instance Firewall)

Now AWS evaluates the **security group attached to the ENI**.

Example rule:
```scss
Inbound rule
TCP 22
Source: 203.0.113.10
```

> Security groups are **stateful**.
> If inbound traffic is allowed, return response-traffic is automatically allowed.

---

## Step 5 — ENI (Elastic Network Interface)

The packet reaches the **network interface attached to the instance**.

The ENI delivers the packet to the instance operating system.

---

## Step 6 — OS Network Stack

Inside the instance:
```scss
ENI
↓
Linux TCP/IP stack
↓
Application port
```

Example:
```scss
Port 22 → SSH daemon
```

Your SSH connection is established.

---

# 3. Return Path (EC2 → Internet)

The response follows a slightly different path:
```plain text
EC2
↓
Security Group (stateful allow)
↓
Network ACL outbound rules
↓
Route table lookup
↓
0.0.0.0/0 → Internet Gateway
↓
Internet Gateway
↓
Internet
↓
Client
```

Notice the key difference:

> **Route table matters here**, because AWS must decide **where to send outbound traffic**.

---

# 4. AWS Evaluation Order

## Inbound Traffic
- Internet Gateway
- Public → Private IP translation
- VPC Router (local route)
- Network ACL
- Security Group
- ENI
- Instance OS

## Outbound Traffic
- Security Group
- Network ACL
- Route table
- Internet Gateway / NAT Gateway


---

# 5. Key Mental Model

Think of networking layers like this:
```scss
Routing decides WHERE traffic goes
Firewalls decide WHETHER it is allowed
```


| Component | Role |
|----------|------|
| Route Table | Decides where traffic goes |
| Network ACL | Subnet firewall |
| Security Group | Instance firewall |

---

# 6. Final Takeaway

Inbound traffic from the internet:

- Does **not rely on** `0.0.0.0/0 → IGW`
- Relies on **public IP mapping + local VPC routing**

Outbound traffic:

- **Must use the route table** to reach **Internet Gateway or NAT Gateway**