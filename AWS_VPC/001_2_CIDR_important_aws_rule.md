# Important AWS Rule
Subnet CIDR must satisfy:
```scss
Subnet CIDR ⊂ VPC CIDR
```

Example (valid):
```scss
VPC: 10.0.0.0/16
Subnet: 10.0.1.0/24
```

Example (invalid):
```scss
VPC: 10.0.0.0/16
Subnet: 192.168.1.0/24 ❌
```

Because it is outside the VPC range.

---

> CIDR blocks are defined at both VPC and subnet levels. The VPC CIDR defines the overall IP address range of the VPC, and subnet CIDRs are smaller ranges allocated from within the VPC CIDR.

---

# CIDR in VPC vs Subnet

CIDR blocks are used to define **IP address ranges** in AWS networking.

They exist at **two levels**:

1. VPC CIDR
2. Subnet CIDR

```scss
10.0.0.0/16   (VPC CIDR)

├── 10.0.1.0/24   Public Subnet
├── 10.0.2.0/24   Private Subnet
├── 10.0.3.0/24   Database Subnet
└── 10.0.4.0/24   Cache Subnet
```
