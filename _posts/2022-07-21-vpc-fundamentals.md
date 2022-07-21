---
layout: post
excerpt_separator: <!--more-->
---

Notes on the AWS Virtual Private Cloud (VPC) service. 

Fundamental features and links to related topics.

Links:

- [AWS VPC docs](https://docs.aws.amazon.com/vpc/?id=docs_gateway)
- [Internet protocol suite](https://en.wikipedia.org/wiki/Internet_protocol_suite)
- [Cloud computing](https://en.wikipedia.org/wiki/Cloud_computing)
- [IPv4](https://en.wikipedia.org/wiki/IPv4)
- [IPv6](https://en.wikipedia.org/wiki/IPv6)
- [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)


## VPC overview

Networks defined in aws cloud

- isolated software defined networking
- custom IPv4/v6 address range
- multiple subnets
- routing table configuration
- network address translation (NAT gateway)
- created with console, CLI, or IaC
- security and firewalls

Security groups

- associated with EC2, allow rules
- stateful

Network ACLs

- associated with VPCs/subnets
- allow and deny rules
- stateless

## VPC deployment options

Plan cloud network

- number and location of networks/subnets
- traffic control in and out
	- remote ssh for admin
	- public web app in subnet port 80/443
- IPv4 address ranges
	- CIDR notation /16 /24
- network isolation/connectivity
	- test vpc sandbox
- resources in network
	- subnet for public front end
	- subnet for private back end database

VPC Deployment Options

- VPC with a single public subnet
- VPC with public and private subnets
- VPC with public and private subnets and hardware VPN
- VPC with private subnet and hardware vpn

VPC with a single public subnet

- create one subnet and internet gateway
- subnet resources are open to the internet
	- elastic and public IPs
- control traffic with ACLs and security groups

VPC with public and private subnets

- one public and one private subnet
- used for multi tier applicatoins
	- public subnet for front end
	- private subnet for back end
- private resources do not have direct outbound internet access
	- NAT in public subnet for indirect

VPC with public and private subnets and hardware VPN

- VPN links on premise network to aws
	- IPsec VPN tunnel
	- aws manages cloud side vpn configuration
	- user must configure on premise vpn
- on premise traffic to aws elastic Ip
	- traverse internet, not VPN
- aws private subnet traffic
	- routed to on premise network via VPN

VPC with private subnet and hardware vpn

- no internet gateway
	- no internet connectivity
- extend on premise network into aws
- vpn links on premise network to cloud
	- IPsec vpn tunnel
	- manage vpn on both ends

## VPC networking components

VPCs will contain a set of components

- subnets
- route tables
- network interfaces
- elastic IP address
- NAT and internet gateways

Subnets

- contained with vpc
- created within an AZ
- ip address range falls within vpc range
	- auto assign ipv4 public ip
- ec2 instances deployed into subnets
- associated with network ACLs
	- allow/deny inbound/outbound network traffic flow
- associated with route tables

Route tables

- routing control
- internet gateways
	- 0.0.0.0/0 default route
- virtual firewall appliances
- on premise networks through vpn or direct connect
- vpc peering
	- private traffic between vpcs

Network interfaces

- elastic network interface
	- attached / detached from instances
- subnet set at create time
- auto mac address
- ip addressing
	- ipv4 or ipv6
	- static or dynamic assignment
- security groups associated with interface

Elastic ip address

- static ipv4 address
- linked to aws account
- associated with
	- ec2 instance
	- network interface
- release ip if not needed
	- cost $ to use

Gateways

- Network address translation (NAT)
	- allow internet connectivity from private subnet
	- connections from internet not allowed
	- only responses from internet allowed
	- modify subnet route tables to use NAT
- Internet gateway
	- provides subnet access to internet
	- connections from internet allowed
	- modify subnet route tables to use internet


## IP addressing and subnets

Plan out ip addressing prior to vpc and resource deployment.

- CIDR notation
- ip address visibility
	- public, reachable from internet
	- private, used within vpcs
- aws supports ipv4 and ipv6
- ip addresses
	- associated with network interfaces
	- statically or dynamically assigned
- ec2 instances recieve an internal dns hostname
	- example: ip-10-23-55-1.ec2.internal
	- resolvable only within vpc sunet
- new ec2 instances
	- receive a private ip from subnet is occupies
	- address constant between reboots
	- address released when instance is terminated
- auto assign public ip option
 

## Create a VPC

Create vpc through aws console

- configuration wizard
- old console style
- ip cidr range and block
- route table and dns
- acl and security group
- subnets


## Automate deployment of infrastructure

Deployment tools

- powershell (lol)
- aws cli
- api, boto3
- cloud formation IaC
- aws elastic beanstalk and ops works

Cloudformation IaC

- template file
	- json with passed parameters
	- custom or preconfigured
- related resources deployed quickly

Beanstalk

- application infrastructure orchestration service
- do not provision individual resources
- upload applications in different languages

OpsWorks

- centralized application cofiguration managment
	- chef and puppet
- no manual ec2 configuration
- automate instance
	- deployment, configuration, and management
- applications consist of
	- stacks, resources
	- layers, configuration


## Security groups and network ACLs

Security group

- stateful firewall
	- supports allow rules
	- tracks state of connection
- associated with network interfaces
	- attached to ec2
- consists of acl allow rules
	- similiar to a traditional firewall
- determine how many security groups
	- firewall pools
	- one group can be associated withn many interfaces
- consistent naming and taging
	- track security 

Network ACLs

- associated with vpc subnets
- support both allow and deny rules
- stateless firewall
	- return traffic is not allowed automatically


## Implement security groups

- ec2 console
- network and security
- security groups
- give name, description, vpc, rules, tags
- type, protocol, port, source
- statefull firewall
	- do not have to define inbound and outbound rules


## Create internet gateway

- allow access to internet for vpc deployed resources
- vpc dashboard
- subnets
- internet gateway
	- state: attached, detached
- route table
	- connect internet gateway to subnet 


## Implement network ACLs

- perimeter in and out bound traffic control
- vpc dashboard
- security
- network ACLs
- name and vpc
- default deny all in and out bound
- edit vpc nacl association


## VPC connectivity options

VPC peering

- connectivity between vpc
- different regions and accounts
- no vpn
- ec2 in different vpc communicate
- single vpc peered with multiple vps
	- no transitive peering













