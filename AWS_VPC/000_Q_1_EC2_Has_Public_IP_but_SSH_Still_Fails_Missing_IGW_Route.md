# EC2 Has Public IP but SSH Still Fails (Missing IGW Route)

This is a subtle networking case in **Amazon VPC** where an **EC2 instance has a public IP**, but SSH still fails because the **subnet route table does not send outbound traffic to the Internet Gateway**.

---

# 1. Scenario Setup

Assume the following:
```scss
EC2 private IP: 10.0.1.10
EC2 public IP: 3.110.40.5
Subnet CIDR: 10.0.1.0/24
```

Route table:
```scss
Destination Target
10.0.0.0/16 local
```

Important:  
There is **no route** to the Internet Gateway.
```scss
0.0.0.0/0 → Internet Gateway
```

---

# 2. Inbound Packet From Internet

Your laptop sends:
```scss
ssh 3.110.40.5
```

Packet path:
```scss
Laptop
↓
Internet
↓
Internet Gateway
↓
Public IP → Private IP translation
↓
Destination becomes 10.0.1.10
```

The Internet Gateway performs:
```scss
3.110.40.5 → 10.0.1.10
```

Now the packet is inside the VPC.

---

# 3. Internal VPC Routing

The VPC router checks the route table.

Every route table automatically includes:
```scss
10.0.0.0/16 → local
```

So AWS knows the packet should stay inside the VPC.
```scss
Destination = 10.0.1.10
```

The router determines the subnet:
```scss
10.0.1.10 ∈ 10.0.1.0/24
```

Packet flow continues:
```scss
VPC Router
↓
Subnet
↓
Network ACL
↓
Security Group
↓
EC2
```

So the **inbound SSH packet actually reaches the EC2 instance**.

---

# 4. EC2 Sends Response (Critical Step)

SSH uses TCP, which requires a reply.

EC2 sends:
```scss
SYN-ACK
```

Destination:
```scss
Your laptop public IP
```

Example:
```scss
203.0.113.20
```

Now AWS must determine where to send this packet.

The router checks the route table.

---

# 5. Route Table Lookup

Destination:
```scss
203.0.113.20
```

Route table:
```scss
10.0.0.0/16 → local
```

But:
```scss
203.0.113.20 NOT in 10.0.0.0/16
```

So there is **no matching route**.

Because the route
```scss
0.0.0.0/0 → Internet Gateway
```

is missing, AWS has **no path to the internet**.

Result:
```scss
Packet dropped
```

---

# 6. Why SSH Fails

TCP requires a 3-way handshake:
```scss
Client → SYN

Server → SYN-ACK

Client → ACK
```

What happens here:
```scss
Client → SYN ✅ reaches EC2
Server → SYN-ACK ❌ dropped
```

Since the response never reaches the client, the SSH connection fails.

---

# 7. Correct Route Table

To fix the issue, the subnet route table must include:
```scss
Destination Target
10.0.0.0/16 local
0.0.0.0/0 Internet Gateway
```

Now the response path becomes:
```scss
EC2
↓
Route table
↓
0.0.0.0/0 → Internet Gateway
↓
Internet Gateway
↓
Internet
↓
Laptop
```

SSH will work.

---

# 8. Visual Comparison

## Broken Case
```scss
Internet
↓
Internet Gateway
↓
EC2
↓
Response
↓
Route table
↓
❌ No route to internet
```

---

## Working Case
```scss
Internet
↓
Internet Gateway
↓
EC2
↓
Route table
↓
0.0.0.0/0 → Internet Gateway
↓
Internet
```

---

# 9. Key Insight

Inbound traffic reaches EC2 because of:


Public IP → Private IP mapping


But outbound traffic must follow the **route table**.

If no route exists, the response cannot leave the VPC.

---

# Final Rule

For an EC2 instance with a public IP to be reachable from the internet:

1. Internet Gateway attached to the VPC  
2. Subnet route table has `0.0.0.0/0 → Internet Gateway`  
3. Security group allows the traffic  
4. Network ACL allows the traffic  

All four must be correctly configured.