# Longest Prefix Match in AWS Route Tables

**Longest Prefix Match** is the rule AWS uses to decide **which route to use when multiple routes match a destination IP address**.

> AWS always chooses the **most specific route (the route with the largest prefix length).**

---

# 1. What is a Prefix?

A prefix refers to the **CIDR block** in the route destination.

Examples:

| CIDR | Prefix Length |
|-----|---------------|
| 0.0.0.0/0 | 0 |
| 10.0.0.0/16 | 16 |
| 10.0.1.0/24 | 24 |

Key idea:
```scss
/24 is more specific than /16
/16 is more specific than /0
```


The **larger the number after "/"**, the **more specific the network**.

---

# 2. Example Route Table

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |
| 10.0.1.0/24 | NAT Gateway |
| 0.0.0.0/0 | Internet Gateway |

---

# 3. Traffic Example

Suppose an EC2 instance sends traffic to:
```scss
10.0.1.5
```

Matching routes:
```scss
10.0.0.0/16
10.0.1.0/24
0.0.0.0/0
```

AWS selects:
```scss
10.0.1.0/24
```

Because **/24 is the longest prefix (most specific)**.

---

# 4. Another Example

Route table:

| Destination | Target |
|-------------|--------|
| 0.0.0.0/0 | Internet Gateway |
| 192.168.0.0/16 | VPN |
| 192.168.1.0/24 | NAT Gateway |

Traffic destination:
```scss
192.168.1.20
```

Matching routes:
```scss
0.0.0.0/0
192.168.0.0/16
192.168.1.0/24


AWS chooses:
```scss
192.168.1.0/24
```

Because **/24 is more specific than /16 and /0**.

---

# 5. Why This Rule Exists

Longest Prefix Match allows **special routing for smaller networks**.

Example:
```scss
0.0.0.0/0 → Internet Gateway
10.0.1.0/24 → Firewall
```

Traffic destined for:
```scss
10.0.1.x
```

will go to the **firewall instead of the internet gateway**.

---

# 6. Visual Example


Route Table

0.0.0.0/0 → Internet Gateway
10.0.0.0/16 → Local
10.0.1.0/24 → Firewall


Traffic:
```scss
10.0.1.10
```

Selected route:
```scss
10.0.1.0/24
```

Because it is **most specific**.

---

# 7. Specificity Order

From **most specific to least specific**:
```scss
/32 → most specific
/24
/16
/8
/0 → least specific
```

---

# 8. Key Rule
```scss
Longest Prefix Match Wins
```

AWS always selects the **route with the most specific CIDR match**.

---

# 9. Interview Answer

AWS route tables follow the **Longest Prefix Match rule**, meaning when multiple routes match a destination IP address, AWS selects the route with the **most specific CIDR block (largest prefix length)**.