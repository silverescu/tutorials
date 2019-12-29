### Create VPC with DHCP option set

### The Dynamic Host Configuration Protocol (DHCP) provides a standard for passing configuration information to hosts on a TCP/IP network. The options field of a DHCP message contains the configuration parameters. Some of those parameters are the domain name, domain name server, and the netbios-node-type.

### Create a new vpc

    name tag        : demo_vpc

    ipv4 cidr block : 10.0.0.0/16

### Create subnets

Public subnet #1

    name tag : public1
    vpc      : demo_vpc
    az       : us-east-1a
    ipv4 cidr: 10.0.1.0/24

Public subnet #2

    name tag : public2
    vpc      : demo_vpc
    az       : us-east-1b
    ipv4 cidr: 10.0.2.0/24

### Launch demo EC2 instance

    OS                   : Amazon Linux 2 AMI
    Instance type        : t2.micro
    Subnet               : public1
    Auto-assign public ip: Enable
    Tags: 
        Name: DemoInstance
    Select security group: default
    Key pair             : demo_lab


### Create new DHCP option set

    Name tag            : new DHCP option set
    Domain name         : example.com
    Domain name server  : AmazonProdivedDNS
    NTP server          : 
    NetBIOS name servers:
    NetBIOS node type   :

### Change the DHCP option set of the VPC

### Launch demo EC2 instance

    OS                   : Amazon Linux 2 AMI
    Instance type        : t2.micro
    Subnet               : public1
    Auto-assign public ip: Enable
    Tags: 
        Name: DemoInstance
    Select security group: default
    Key pair             : demo_lab

### Verify new private DNS 

### Documentation

- [AWS VPC DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html)



