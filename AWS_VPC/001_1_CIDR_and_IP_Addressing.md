# CIDR (Classless Inter-Domain Routing)

**CIDR** is a method used to represent **IP address ranges** and divide networks into smaller subnets.

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
- `/16` → Prefix length (number of bits used for the network)

---

# 1. Why CIDR Exists

Before CIDR, networks used **fixed classes** (Class A, B, C).  
CIDR allows **flexible network sizes** and more efficient IP allocation.

Benefits:

- Efficient use of IP addresses
- Flexible subnetting
- Better routing in large networks

---

# 2. CIDR Structure

Example:
```scss
192.168.1.0/24
```

Meaning:

- First **24 bits** → Network part
- Remaining **8 bits** → Host part

```scss
Network bits | Host bits
192.168.1 | 0-255
```


---

# 3. Example CIDR Blocks

| CIDR | Total IPs |
|-----|-----------|
| /32 | 1 |
| /30 | 4 |
| /24 | 256 |
| /16 | 65,536 |
| /8 | 16,777,216 |

Formula:
```scss
Number of IPs = 2^(32 - prefix)
```

Example:
```scss
/24
2^(32-24) = 256 IP addresses
```

---

# 4. AWS VPC Example

When creating a VPC, you define a **CIDR block**.

Example:
```scss
VPC CIDR: 10.0.0.0/16
```

Range of IPs:
```scss
10.0.0.0 → 10.0.255.255
```

You can then create subnets inside it.

Example:
```scss
10.0.0.0/16 (VPC)

10.0.1.0/24 (Subnet A)
10.0.2.0/24 (Subnet B)
10.0.3.0/24 (Subnet C)
```

---

# 5. Private CIDR Ranges

These ranges are reserved for **private networks** (used in VPCs).

| CIDR Range | Size |
|---|---|
| 10.0.0.0/8 | Large networks |
| 172.16.0.0/12 | Medium networks |
| 192.168.0.0/16 | Small networks |

Example AWS VPC CIDR:
```scss
10.0.0.0/16
172.31.0.0/16
192.168.0.0/16
```

---

# 6. Visual Example
```scss
10.0.0.0/16 (VPC)

├── 10.0.1.0/24 Public Subnet
├── 10.0.2.0/24 Private Subnet
└── 10.0.3.0/24 Database Subnet
```


Each subnet has **256 IP addresses**.

---

# 7. Important AWS Rule

AWS reserves **5 IP addresses per subnet**.

Example:
```scss
10.0.1.0/24
```

Reserved:
```scss
10.0.1.0 → Network address
10.0.1.1 → AWS router
10.0.1.2 → DNS
10.0.1.3 → Reserved
10.0.1.255 → Broadcast
```

Usable IPs:
```scss
256 - 5 = 251
```
