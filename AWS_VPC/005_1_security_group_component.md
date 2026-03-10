The following are the components of inbound and outbound security group rules:

**Protocol**: The protocol to allow. The most common protocols are 6 (TCP), 17 (UDP), and 1 (ICMP).

**Port range**: For TCP, UDP, or a custom protocol, the range of ports to allow. You can specify a single port number (for example, 22), or range of port numbers (for example, 7000-8000).

**ICMP type and code**: For ICMP, the ICMP type and code. For example, use type 8 for ICMP Echo Request or type 128 for ICMPv6 Echo Request. For more information, see Rules for ping/ICMP in the Amazon EC2 User Guide.

**Source or destination**: The source (inbound rules) or destination (outbound rules) for the traffic to allow. Specify one of the following:
- A single IPv4 address. You must use the /32 prefix length. For example, 203.0.113.1/32.
- A single IPv6 address. You must use the /128 prefix length. For example, 2001:db8:1234:1a00::123/128.
- A range of IPv4 addresses, in CIDR block notation. For example, 203.0.113.0/24.
- A range of IPv6 addresses, in CIDR block notation. For example, 2001:db8:1234:1a00::/64.
- The ID of a prefix list. For example, pl-1234abc1234abc123. For more information, see Managed prefix lists.
- The ID of a security group. For example, sg-1234567890abcdef0. For more information, see Security group referencing.

**(Optional) Description**: You can add a description for the rule, which can help you identify it later. A description can be up to 255 characters in length. Allowed characters are a-z, A-Z, 0-9, spaces, and ._-:/()#,@[]+=;{}!$*.