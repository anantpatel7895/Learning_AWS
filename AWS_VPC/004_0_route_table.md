[Route Table documentation](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)


# What is route table

A route table contains a set of rules, called routes, that are used to determine where network traffic from your VPC is directed. 

> Association means linking a route table to a subnet.

# Important Rules

- One subnet → only one route table
- One route table → many subnets

# IMPORTANT POINT
- When you create a VPC, AWS automatically creates one **Main Route Table**.
- Each VPC can have multiple Route Table along with Main Route Table.
- if a subnet is not explicitly associated(linked) with any route table, it will use the **main route table**.
- A subnet can be associated(linked) with **only one route table at a time**, but one route table can be associated(linked) with **multiple subnets**.


