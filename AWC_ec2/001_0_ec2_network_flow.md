# EC2 Network Flow

```scss
Your Laptop
   ↓
Internet
   ↓
Internet Gateway
   ↓
VPC Router checks Route Table
   ↓
Routes packet to correct Subnet
   ↓
Network ACL
   ↓
Security Group
   ↓
ENI (eth0)
   ↓
Linux TCP/IP stack
   ↓
SSH daemon
   ↓
Application (SSH / Nginx / App)
```