# VPC - Virtual Private Cloud

Think of VPC as virtual data center in the cloud!

## VPC Definition

Amazon Virtual Private Cloud (Amazon VPC) lets you provision a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define.

You have complete control over you virtual network environment, including selection of your own IP address range, creation of subnets and config of route tables and network gateways. **VPC now allows customers to expand their VPCs by adding secondary IPv4 address ranges (CIDRs) to their VPCs.**

You can easily customize the network config for your VPC. For example, you can create a public facing subnet of your webservers that has access to the internet, and place your backend systems such as databases or application servers in a private-facing subnet with no internet access.

You can leverage multiple layers of security, including security groups and network access control lists, to help control access to EC2 instances on each subnet.

Additionally, you can create a Hardware Virtual Private Network (VPN) connection between your corporate datacenter and your VPC and leverage the AWS cloud as an extension of your corporate datacenter.

**NOTE:** Private and public subnets within a VPC can only have one subnet per AZ

Use (cidr.xyz)[https://cidr.xyz/] to figure out subnet ranges within a VPC

### What can you do with a VPC?

- Launch instances into a subnet of your choosing
- Assign custom IP address ranges in each subnet
- Configure route tables between subnets
- Create single internet gateway and attach it to our VPC
- Much better security control over your AWS resources
- Instance security groups
- Subnet network access control lists (ACLS)

### Default VPC vs Private VPC

- Default VPC is user friendly, allowing you to immediately deploy instances
- All subnets have a route to internet
- No private subnets in default VPC
- EC2 instance has both a public and private IP address.

### VPC Peering

- Allows you to connect one VPC with another via a direct network route using private IP addresses
- Instances behave as if they are on the same private network.
- You can peer VPCs with other AWS accounts as well as with other VPCs in the same account
 -Inter-region VPC peering connections allow secure communication between VPC resources in different AWS Regions
- Peering is in a star config: ie 1 central VPC peers with 4 others. NO TRANSITIVE PEERING!!

### VPC Endpoint
- VPC Endpoint always takes precedence over NAT Gateway or Internet Gateway. In the absence of VPC endpoint, requests to S3 are routed to NAT Gateway or Internet Gateway based on their existence in route table.
- VPC endpoints does not support cross-region S3 requests. So, if S3 bucket is in different region than the VPC, then VPC endpoint does not take effect. In this case, requests will go through NAT Gateway as the route table has a route to it.
- VPC endpoint has a policy which by default allows all actions on all S3 bucket. We can restrict access to certain S3 buckets and certain actions on this policy. In such cases, for accessing any new buckets or for any new actions, VPC endpoint policy needs to be modified accordingly.
- Endpoint connections cannot be extended out of a VPC. Resources on the other side of a VPN connection, VPC peering connection, AWS
Direct Connect connection, or ClassicLink connection in your VPC cannot use the endpoint to communicate with resources in the endpoint service.

**LIMITATIONS:**
- Interface Endpoints currently does not support endpoint policies. Services are allowed with full access.
- Interface Endpoints currently supports only TCP traffic.
- We cannot tag Endpoints, like the way we do with EC2 instance.
- Endpoints are only supported within the same region. You cannot use endpoints to connect a service from one region to a VPC in a different region.
- Endpoints currently supports only IPv4 traffic.
- An endpoint once created cannot be transferred from one VPC to another or to a different service.
- In a VPC peering connection, using NAT Gateway of another VPC becomes transitive routing and is not supported in AWS.



# NAT - Network Address Translation 

### NAT Instances

- When creating a NAT instance, Disable Source/Destination Check on the instance.
- NAT instances must be in a public subnet
- There must be a route out of the private subnet to the NAT, in order for this to work.
- The amount of traffic that NAT instances can support depends on the instance size. If you are bottlenecking, increase the instance size.
- You can create high availability using Autoscaling Groups, multiple subnets in different AZs, and a script to automate failover.
- Behind a Security Group.

### NAT Gateways

- Preferred by the enterprise
- Scale automatically up to 10G
- No need to patch OS
- Not associated with security groups
- Automatically assigned public IP
- Must update root tables and point them to NAT Gateway
- Having one NAT Gateway in one AZ is not good enough, must me redundant in multiple AZs
- No need to disable Source/Destination Checks
- More Secure than NAT Instance
- A NAT gateway cannot send traffic over: VPC endpoints, VPN connection, AWS Direct Connect and VPC peering connections.

## NACL - Network Access Control Lists

- Can only associate **1** subnet to a Network ACLs
- Your VPC automatically comes with a default NACL, and by default it allows all inbound and outbound and traffic.
- You can create custom NACLs. By default each custom NACL denies all inbound and outbound trafic until you add rules
- Each subnet in your VPC must be associated with a network ACL. If you dont explicitly associate a subnet with a network ACL, the subnet is automatically associated with the default NACL.
- You can associate a NACL with multiple subnets; however, a subnet can be associated with only one network ACL at a time. When you associate a NACL with a subnet, the previous association is removed.
- NACLs contain a numbered list of rules that is evaluated in order, starting with the lowest numbered rule
- NACL have separate inbound and outbound rules, and each rule can either allow or deny traffic.
- NACL are stateless; responses to allowed inbound traffic are subject to the rules for outbund traffic

## ALB

- You need at least 2 public subnets in order to deply and application load balancer

## VPC Flow Logs

VPC Flow Logs is a feature that enables you to capture info about the IP traffic going to and from network interfaces in your VPC. Flow log data is stored using Amazon Cloudwatch Logs.

After you've created a flow log, you can view and retrieve its data in Amazon CloudWatch Logs.

Flow logs can be create at 3 levels:

- VPC
- Subnet
- Network Interface Level
