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


**YAML Code:**

---
AWSTemplateFormatVersion: "2010-09-09"

Description: This template creates VPC with public and private subnets.

Metadata:
  AWS::CloudFormation::Interface:
    ParameterGroups:
      -
        Label:
          default: "VPC CICR"
        Parameters:
          - vpcCIDR
      -
        Label:
          default: "Subnet CICR"
        Parameters:
          - PublicSubnet1CIDR
          - PublicSubnet2CIDR
          - PrivateSubnet1CIDR
          - PrivateSubnet2CIDR
          - PrivateSubnet3CIDR
          - PrivateSubnet4CIDR
      -
        Label:
          default: "SSH CICR"
        Parameters:
          - SSHLocation
  
Parameters:
  vpcCIDR:
    Default: 10.0.0.0/16
    Description: Please enter the IP range (CIDR notaion)
    Type: String

  PublicSubnet1CIDR:
    Default: 10.0.0.0/24
    Description: Please enter the IP range (CIDR notaion) for Public1.
    Type: String

  PublicSubnet2CIDR:
    Default: 10.0.1.0/24
    Description: Please enter the IP range (CIDR notaion) for Public2.
    Type: String
  
  PrivateSubnet1CIDR:
    Default: 10.0.2.0/24
    Description: Please enter the IP range (CIDR notaion) for private1.
    Type: String

  PrivateSubnet2CIDR:
    Default: 10.0.3.0/24
    Description: Please enter the IP range (CIDR notaion) for private2.
    Type: String

  PrivateSubnet3CIDR:
    Default: 10.0.4.0/24
    Description: Please enter the IP range (CIDR notaion) for private3.
    Type: String

  PrivateSubnet4CIDR:
    Default: 10.0.5.0/24
    Description: Please enter the IP range (CIDR notaion) for private4.
    Type: String

  SSHLocation:
    AllowedPattern: '(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})/(\d{1,2})'
    ConstraintDescription: must be a valid IP CIDR range of the form x.x.x.x/x.
    Default: 0.0.0.0/0
    Description: The IP address range that can be used to access the web server using SSH.
    MaxLength: '18'
    MinLength: '9'
    Type: String

Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref vpcCIDR
      EnableDnsHostnames: true
      EnableDnsSupport: true
      InstanceTenancy: default

  Internetgateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          value: Test IGW
      

  InternetGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      InternetGatewayId: !Ref Internetgateway
      VpcId: !Ref VPC
      
  PublicSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      AvailabilityZone: !Select [0, !GetAZs '']
      CidrBlock: !Ref PublicSubnet1CIDR
      MapPublicIpOnLaunch: true
      VpcId: !Ref VPC

  PublicSubnet2:
    Type: AWS::EC2::Subnet
    Properties:
      AvailabilityZone: !Select [1, !GetAZs '']
      CidrBlock: !Ref PublicSubnet2CIDR
      MapPublicIpOnLaunch: true
      VpcId: !Ref VPC

  PublicRouteTable:
    Type: AWS::EC2::Routetable
    Properties:
      VpcId: !Ref VPC

  PublicRoute:
    Type: AWS::EC2::Route
    Properties:
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref Internetgateway
      RouteTableId: !Ref PublicRouteTable

  PublicSubnet1RouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref PublicSubnet1

  PublicSubnet2RouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref PublicSubnet2

  PrivateSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      AvailabilityZone: !Select [0, !GetAZs '']
      CidrBlock: !Ref PrivateSubnet1CIDR
      MapPublicIpOnLaunch: false
      VpcId: !Ref VPC

  PrivateSubnet2:
    Type: AWS::EC2::Subnet
    Properties:
      AvailabilityZone: !Select [1, !GetAZs '']
      CidrBlock: !Ref PrivateSubnet2CIDR
      MapPublicIpOnLaunch: false
      VpcId: !Ref VPC

  PrivateSubnet3:
    Type: AWS::EC2::Subnet
    Properties:
      AvailabilityZone: !Select [2, !GetAZs '']
      CidrBlock: !Ref PrivateSubnet3CIDR
      MapPublicIpOnLaunch: false
      VpcId: !Ref VPC

  PrivateSubnet4:
    Type: AWS::EC2::Subnet
    Properties:
      AvailabilityZone: !Select [3, !GetAZs '']
      CidrBlock: !Ref PrivateSubnet4CIDR
      MapPublicIpOnLaunch: false
      VpcId: !Ref VPC

  ALBSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow http to client host
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
      SecurityGroupEgress:
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 0.0.0.0/0

  SSHSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH to client host
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: !Ref SSHLocation

  WebserverSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow static Webseite
      VpcId: !Ref VPC
      SecurityGroupIngress:
         - IpProtocol: tcp
           FromPort: 80
           ToPort: 80
           SourceSecurityGroupId: !Ref ALBSecurityGroup
         - IpProtocol: tcp
           FromPort: 443
           ToPort: 443
           SourceSecurityGroupId: !Ref ALBSecurityGroup
         - IpProtocol: tcp
           FromPort: 22
           ToPort: 22
           SourceSecurityGroupId: !Ref ALBSecurityGroup

  dataserverSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Open DB
      VpcId: !Ref VPC
      SecurityGroupIngress:
         - IpProtocol: tcp
           FromPort: 3306
           ToPort: 3306
           SourceSecurityGroupId: !Ref WebserverSecurityGroup

Outputs:
  VPC: 
    Description: VPC ID
    Value: !Ref VPC
    Export:
      Name: !Sub ${AWS::StackName}-VPC

  PublicSubnet1: 
    Description: PublicSubnet 1 ID
    Value: !Ref PublicSubnet1
    Export:
      Name: !Sub ${AWS::StackName}-PublicSubnet1
  
  PublicSubnet2: 
    Description: PublicSubnet 2 ID
    Value: !Ref PublicSubnet2
    Export:
      Name: !Sub ${AWS::StackName}-PublicSubnet2

  PrivateSubnet1: 
    Description: PrivateSubnet1 ID
    Value: !Ref PrivateSubnet1
    Export:
      Name: !Sub ${AWS::StackName}-PrivateSubnet1

  PrivateSubnet2: 
    Description: PrivateSubnet2 ID
    Value: !Ref PrivateSubnet2
    Export:
      Name: !Sub ${AWS::StackName}-PrivateSubnet2
  
  PrivateSubnet3: 
    Description: PrivateSubnet3 ID
    Value: !Ref PrivateSubnet3
    Export:
      Name: !Sub ${AWS::StackName}-PrivateSubnet3

  PrivateSubnet4: 
    Description: PrivateSubnet4 ID
    Value: !Ref PrivateSubnet4
    Export:
      Name: !Sub ${AWS::StackName}-PrivateSubnet4

  ALBSecurityGroup: 
    Description: ALB Security Group ID
    Value: !Ref ALBSecurityGroup
    Export:
      Name: !Sub ${AWS::StackName}-ALBSecurityGroup

  SSHSecurityGroup: 
    Description: SSH Security Group ID
    Value: !Ref SSHSecurityGroup
    Export:
      Name: !Sub ${AWS::StackName}-SSHSecurityGroup

  WebserverSecurityGroup: 
    Description: WebserverSecurityGroup ID
    Value: !Ref WebserverSecurityGroup
    Export:
      Name: !Sub ${AWS::StackName}-WebserverSecurityGroup

  dataserverSecurityGroup: 
    Description: dataserverSecurityGroup ID
    Value: !Ref dataserverSecurityGroup
    Export:
      Name: !Sub ${AWS::StackName}-dataserverSecurityGroup

 
![template1-designer (1)](https://github.com/ramutc/VPCsetup/assets/151390614/bfdcfcc1-a6f2-498c-bfab-7ed8baa8c47f)

  
