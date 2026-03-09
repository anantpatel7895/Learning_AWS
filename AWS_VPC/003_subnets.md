# Public vs Private Subnet

## Public Subnet

Route table:
```scss
0.0.0.0/0 → Internet Gateway
```
> Instances can access the internet.

--- 

## Private Subnet

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