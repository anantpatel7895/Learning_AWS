**Main route table** —The route table that automatically comes with your VPC. It controls the routing for all subnets that are not explicitly associated with any other route table.

**Custom route table** —A route table that you create for your VPC.

**Destination** —The range of IP addresses where you want traffic to go (destination CIDR). For example, an external corporate network with the CIDR 172.16.0.0/12.

**Target** —The gateway, network interface, or connection through which to send the destination traffic; for example, an internet gateway.

**Local route** —A default route for communication within the VPC. If the VPC has both IPv4 and IPV6 addresses, there is a local route for IPv4 and a local route for IPv6.

**Route table association** —The association between a route table and a subnet, internet gateway, or virtual private gateway.

**Subnet route table** —A route table that's associated with a subnet.

**Propagation** —If you've attached a virtual private gateway to your VPC and enable route propagation, we automatically add routes for your VPN connection to your subnet route tables. This means that you don't need to manually add or remove VPN routes. For more information, see Site-to-Site VPN routing options in the Site-to-Site VPN User Guide.

**Gateway route table** —A route table that's associated with an internet gateway or virtual private gateway.

**Edge association** —A route table that you use to route inbound VPC traffic to an appliance. You associate a route table with the internet gateway or virtual private gateway, and specify the network interface of your appliance as the target for VPC traffic.

**Transit gateway route table** —A route table that's associated with a transit gateway. For more information, see Transit gateway route tables in Amazon VPC Transit Gateways.

**Local gateway route table** —A route table that's associated with an Outposts local gateway. For more information, see Local gateways in the AWS Outposts User Guide.
