# [VPC](../README.md)

## CIDR, Public and Private IP

### CIDR

* Classless Inter-Domain Routing
* Used in SGs or AWS networking in general
* Structure: (Base IP)/(Subnet mask)
	* /32 := 2^0 = 1 IP
	* /31 := 2^1 = 2 IP
	* /30 := 2^2 = 4 IP

### Private IP

* IANA (Internet Assigned Numbers Authority)
* Private IP ranges
	* `10.0.0.0` - `10.255.255.255` _(10.0.0.0/8)_
	* `172.16.0.0` – `172.31.255.255` _(172.16.0.0/12) AWS Default_
	* `192.168.0.0` – `192.168.255.255` _(192.168.0.0/16)_

## Default VPC

* All new accounts have a default VPC
* New instances are launched into default VPC if none specified
* Have internet connectivity and all instances have public IPs
* Also get a public and private DNS for instances
* Contents
	* 1 VPC
	* 3 Subnets
	* 1 Internet GW
	* 1 DHCP Options Set
	* 1 Network ACL
	* 1 Security group

## VPC Overview

* Soft limit: 5 VPC per region (can be increased)
* Each CIDR
	* Min size: `/28` –> 16 Addresses
	* Max size: `/16` –> 65536 Addresses
* Each VPC can have a maximum of 5 CIDRs (1 primary, 4 secondary)
* Before being able to delete a VPC need to:
	* Terminate all instances
	* Delete all subnets
	* Delete custom security groups and custom route tables
	* Detach any internet gateways or virtual private gateways

## Subnet Overview

* 1 Subnet is tied to 1 AZ
* You can have multiple subnets in each AZ
* Each subnet
	* First `4` IP reserved by AWS AND Last `1` IP reserved by AWS
	* 10.0.0.0/16
		* `10.0.0.0`: Network address
		* `10.0.0.1`: AWS VPC Router
		* `10.0.0.2`: AWS DNS server
		* `10.0.0.3`: Reserved for future use
		* `10.0.0.255`: Broadcast address (But AWS doesn't support broadcast messages)
> Need 29 IPs, what should be the subnet size? –> /27 (32 IPs) is NOT GOOD!
Need at least /26. (/26 –> 64 - 5 = 59 IPs)

## Internet gateway

* Helps VPC instances to connect with the internet
* Scales horizontally, Highly Available and redundant
* Must be created separately from the VPC
* 1 to 1 connection between VPC and IGW
* IGW is also a NAT for instances with Public IP
* __Route tables are required to access the internet, just an IGW is not enough__

### Route table

* Need to associate subnets if not using the Main RT
* Public RT
	* VPC CIDR –> local
	* Anything else (`0.0.0.0/0`) –> Internet Gateway
* Private RT
	* VPC CIDR –> local
	* Anything else (`0.0.0.0/0`) –> NAT Gateway

## NAT

* Network Address Translation
* Allow instances in the private subnets to connect to the internet

### NAT Instances

* Outdated, not recommended
* It is an EC2 instance
* AMI is managed by Amazon, pre-configured
* Not HA, not resilient
* Internet traffic bandwidth depends on instance type
* Must be launched in the Public subnet
* Must disable EC2 flag: `Source/Destination check`
* Must have EIP attached to it
* Route table must be configured to route traffic from private subnets through it
* Must create a SG for it
	* SSH (22)
	* HTTP (80) – __Only from VPC CIDR__
	* HTTPS (443) – __Only from VPC CIDR__
	* All ICMP - IPv4 (0-65535) – __Only from VPC CIDR__

### NAT Gateway

* AWS managed, higher bandwidth, better availability, no admin
* Pay by the hour + bandwidth
* Created in a specific AZ, uses an EIP
* Requires an IGW _(Private instance –> NGW –> IGW)_
* Cannot be used by an instance in the same subnet that it is deployed
* 5 GB/s bandwidth (default) that scales automatically to 45 GB/s
* No need to manage SGs

## DNS Resolution

* `enableDnsSupport`: DNS Resolution setting
	* Default: true
	* Helps decide if DNS resolution is supported for the VPC
	* If true, it queries the AWS DNS server at `169.254.169.253`
* `enableDnsHostname`: DNS Hostname setting
	* Default: false (for newly created VPCs, but true for default)
	* Won't do anything unless `enableDnsSupport` is also true
	* If true, assigns a public hostname to EC2 instances if it has a public IP
* If you use a custom DNS domain names in a private zone R53, both must be set to true

## Network ACL (vs Security groups)

* Network ACL is on the subnet level
* Security groups are on the instance level
* Evaluation order
	* Network ACL Inbound rules
	* Security group Inbound rules
		* Request reached the instance
	* Outbound traffic allowed _(stateful)_
		* Even if there is a Deny for that outbound rule, because it was allowed in, the outbound will always be allowed!
	* Network ACL Outbound rules _(stateless, aka always evaluated)_
* Acts as a firewall on the subnet level
* Default Network ACL: allows every inbound and outbound
* Automatically applies to every instance
* 1 Network ACL per subnet
* New subnets are associated with the default Network ACL
* Network ACL rules
	* Have a number (`1-32766`), with lower number having higher priorities
	* Last rule is an asterisk (*) which is a DENY –> Denies all that didn't match any rule
	* AWS Recommends to add rules with 100 increments
* The rule with the lowest number that matched will be used (aka Not all rules will be evaluated)
* Newly created Network ACLs will deny everything
* Ephemeral ports must be opened if the ACL is restrictive (High numbered ports for TCP connection)

## VPC Peering

* Connect 2 VPCs privately using AWS's network
* Can be different account / different region
* The VPCs must have non-overlapping CIDRs
* A VPC Connection is __not transitive__ (must be established for each VPC that you want to connect)
	* A <–> B <–> C : A and C is not connected unless you also have an A <–> C VPC Peering
* Once a VPC Connection is created, it needs to be accepted
* Must update each VPC's each subnet's route table
	* A: when target IP is VPC B, route traffic to the VPC Peering Connection X
	* B: when target IP is VPC A, route traffic to the VPC Peering Connection X

## VPC Endpoints

* Provides access to AWS services using a private network
* Scale horizontally and are redundant
* Doesn't require NAT, IGW etc... in order to access AWS services (e.g: S3, CW, DDB, API GW...)
* 2 types
	* Interface
		* Provisions an ENI (Private IP address) as an entry point
		* __Must attach security group__
	* Gateway
		* Provisions a target and must be used in a route table
		* S3, Dynamo DB
* In case of issues
	* Check DNS Setting Resolution in the VPC
	* Check route tables (for Gateway endpoints)
	* Gateway
		* S3 Endpoint is set up for eu-west-1
		* __Instance in private subnet using AWS CLI must set `--region eu-west-1`!__

## Flow logs

* Captures information about IP traffic that is going into your interfaces
	* VPC Flow logs
	* Subnet Flow logs
	* Elastic Network Interface logs
* Helps to monitor & troubleshoot connectivity issues
* Logs can go directly to S3 / CloudWatch Logs
* Captures information from AWS managed interfaces too: ELB, RDS, ElastiCache, Redshift, WorkSpaces
* Syntax
	* `<version> <account-id> <interface-id> __<srcaddr>__ __<dstaddr>__ __<srcport>__ __<dstport>__ <protocol> <packets> <bytes> <start> <end> __<action>__ <log-status>`
	* `srcaddr` and `dstaddr`: Identify problematic IPs
	* `srcport` and `dstport`: Identify problematic Ports
	* `action`: ACCEPT/REJECT due to Security groups/Network ACL
* Can be used for analytics on usage patterns, malicious behavior
* S3 –> Athena, CloudWatch Logs –> CloudWatch Insights

## Bastion hosts

* An instance in the public subnet that can be used to SSH into instances in the private subnets
* Security Group rules must be tight
	* Only allow 22 from your IPs
	* Don't allow other instances

## Site to Site VPN

* `Site-to-Site VPN connection` to connect the `VPN gateway` with the `Customer gateway`
* Virtual Private Gateway (aka VPN Gateway)
	* VPN concentrator on AWS side
	* VGW is created and attached to the VPC from which you want to create the S2S connection
	* Possibility to customize ASN
* Customer Gateway
	* Software or Hardware on customer side
	* On premise
	* There are a list of tested devices
	* Requires static, internet-routable IP address
		* If behind corporate NAT (with NAT-T), use public IP of the NAT

## Direct connect & Direct connect GW

* Similar to Site to Site, but this is over private network
* Dedicated, private connection to your VPC
* Connection must be set up between your DC and one of AWS Direct Connect location
* Need to set up a Virtual Private Gateway on your VPC
* Access public resources (S3) and private (EC2) on the same connection
* Use cases
	* Increase bandwidth throughput and save costs - working with large data sets
	* More consistent network experience - applications using real-time data feeds
	* Hybrid environment (on prem + cloud)
* Supports both IPv4 and IPv6

### Direct connect gateway

* When you want to connect to multiple VPCs in many different regions __but same account for now__
* VPCs must have non-overlapping CIDRs
* It won't peer the VPCs just give you access to each

## Egress only Internet gateway

* Only for IPv6
* Similar to NAT (but NAT is for IPv4)
* There are no private IPs in IPv6 (aka all IPs are public)
* Egress Only IGW will give access to Internet to IPv6 instances, but they won't be accessible from the internet
* After creating one, need to update the Route Tables

## [Back](../README.md)