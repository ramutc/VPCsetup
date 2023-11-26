# VPCsetup

This CloudFormation template is designed to create a VPC with public and private subnets, along with associated resources like internet gateways, route tables, security groups, and subnet associations. Let's break down the key sections and resources within this template:

**Parameters**
This section defines input parameters that can be configured when deploying the stack. These parameters include:

**vpcCIDR**: Defines the CIDR block for the VPC.
Subnet CIDR blocks for public and private subnets (PublicSubnet1CIDR, PublicSubnet2CIDR, PrivateSubnet1CIDR, PrivateSubnet2CIDR, PrivateSubnet3CIDR, PrivateSubnet4CIDR).

**SSHLocation**: Specifies the allowed IP range for SSH access to instances within the VPC.

**Resources**
This section creates various AWS resources necessary for the VPC setup:

**VPC**: Defines the Virtual Private Cloud with the specified CIDR block and DNS settings.
**Internet Gateway:** Creates an internet gateway and assigns a name tag to it.
**InternetGatewayAttachment:** Attaches the internet gateway to the VPC. 

**PublicSubnet1 and PublicSubnet2:** Create public subnets in different availability zones with auto-assignment of public IP addresses (MapPublicIpOnLaunch: true).
PublicRouteTable, PublicRoute, 

**PublicSubnet1RouteTableAssociation & PublicSubnet2RouteTableAssociation**: Set up a public route table and associate it with public subnets to enable internet access.

**PrivateSubnet1, PrivateSubnet2, PrivateSubnet3, PrivateSubnet4**: Create private subnets in different availability zones without auto-assigning public IP addresses.

**ALBSecurityGroup, SSHSecurityGroup, WebserverSecurityGroup, dataserverSecurityGroup:** Define security groups allowing specific inbound/outbound traffic for various purposes like load balancers, SSH, web servers, and database servers.

**Outputs:**
Defines the outputs (IDs) of the created resources, making them available for use in other CloudFormation stacks or resources through exports.

**Explanation**
This CloudFormation template sets up a VPC architecture with:

Public subnets suitable for resources that need direct internet access.
Private subnets suitable for resources that shouldn't be directly accessible from the internet (such as databases or application servers).
Security groups to control traffic between resources within the VPC.

 
![template1-designer (1)](https://github.com/ramutc/VPCsetup/assets/151390614/bfdcfcc1-a6f2-498c-bfab-7ed8baa8c47f)

  
