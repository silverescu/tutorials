### Create multi-subnet VPC and secure access to private servers and outbound internet access for updates

### Amazon Virtual Private Cloud (Amazon VPC) enables you to launch AWS resources into a virtual network that you've defined. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS.


### Create a new vpc

    name tag        : test_vpc

    ipv4 cidr block : 10.0.0.0/16

### Create subnets

    
Public subnet #1

    name tag : public1
    vpc      : test_vpc
    az       : us-east-1a
    ipv4 cidr: 10.0.1.0/24

Public subnet #2

    name tag : public2
    vpc      : test_vpc
    az       : us-east-1b
    ipv4 cidr: 10.0.2.0/24

Private subnet #1

    name tag : private1
    vpc      : test_vpc
    az       : us-east-1a
    ipv4 cidr: 10.0.10.0/24

Private subnet #2

    name tag : private2
    vpc      : test_vpc
    az       : us-east-1b
    ipv4 cidr: 10.0.11.0/24

### Create & atach internet gateway

Name tag : test_igw

### Create public route table

Name tag : public_rt

Associate the two subnets named public1 and public2 

Add public route

    0.0.0.0/0 -> test_igw

### Create private route table

Name tag : private_rt

Associate the two subnet named private1 and private 2

### Create and configure security groups

Name tag: bastion_sg
    
    Type       : SSH
    Protocol   : TCP
    Port range : 22
    Source     : My IP
    Description: remote access

Name tag: private_sg
    
    Type       : SSH
    Protocol   : TCP
    Port range : 22
    Source     : bastion_sg
    Description: ssh from bastion host
    
    

### Configure our public and private instances

Public EC2
    
    OS                   : Amazon Linux 2 AMI
    Instance type        : t2.micro
    Subnet               : public1
    Auto-assign public ip: Enable
    Tags: 
        Name: BastionHost
    Select security group: bastion_sg
    Key pair             : lab_test

Private EC2
    
    OS                   : Amazon Linux 2 AMI
    Instance type        : t2.micro
    Subnet               : private1
    Auto-assign public ip: Disable
    Tags: 
        Name: AppServer
    Select security group: private_sg
    Key pair             : lab_test

### Check ssh access to the private instance

    chmod 400 lab_test.pem
    ssh-add ~/Downloads/lab_test.pem
    ssh-add -L

    ssh -A ec2-user@x.x.x.x
    ssh ec2-user@x.x.x.x

### Configure a NAT Gateway 

Launch a nat gateway using the public2 subnet

Add a route to the internet in private_rt with the nat gateway as destination  

### Secure our infrastructure with NACL's

Name tag: public1

Associate public1 subnet with public1 nacl
    
    Allow SSH from my ip

    inbound rules
    
        Rule #    : 100
        Type      : SSH
        Protocol  : TCP(6)
        Port range: 22
        Source    : x.x.x.x/32 (replace x.x.x.x with your public IP)
        Allow/Deny: Allow

    outbound rules
    
        Rule #     : 100
        Type       : Custom TCP rule
        Protocol   : TCP(6)
        Port range : 1024 - 65535 (ephemeral port range)
        Destination: x.x.x.x/32 (replace x.x.x.x with you machine public IP)
        Allow/Deny : Allow

    Allow SSH in to our VPC
    inbound rules
    
        Rule #    : 110
        Type      : Custom TCP rule
        Protocol  : TCP(6)
        Port range: 1024 - 65535 (ephemeral port range)
        Source    : 10.0.0.0/16 
        Allow/Deny: Allow
    
    outbound rules
    
        Rule #     : 110
        Type       : SSH
        Protocol   : TCP(6)
        Port range : 22
        Destination: 10.0.0.0/16
        Allow/Deny : Allow
    
    Allow http/https traffic for updates
    
    inbound rules
    
        Rule #    : 120
        Type      : Custom TCP rule
        Protocol  : TCP(6)
        Port range: 1024 - 65535 (ephemeral port range)
        Source    : 0.0.0.0/0
        Allow/Deny: Allow
    
    outbound rules
    
        Rule #     : 120
        Type       : HTTP(80)
        Protocol   : TCP(6)
        Port range : 80
        Destination: 0.0.0.0/0
        Allow/Deny : Allow
    
        Rule #     : 130
        Type       : HTTPS(443)
        Protocol   : TCP(6)
        Port range : 443
        Destination: 0.0.0.0/0
        Allow/Deny : Allow

name tag: private

associate the two private subnet with private nacl
    
    Allow SSH from vpc cidr
 
    inbound rules
 
        Rule #    : 100
        Type      : SSH
        Protocol  : TCP(6)
        Port range: 22
        Source    : 10.0.0.0/16
        Allow/Deny: Allow

    outbound rules
 
        Rule #     : 100
        Type       : Custom TCP rule
        Protocol   : TCP(6)
        Port range : 1024 - 65535 (ephemeral port range)
        Destination: 10.0.0.0/16
        Allow/Deny : Allow

    Allow http traffic for updates
 
    inbound rules
 
        Rule #    : 110
        Type      : Custom TCP rule
        Protocol  : TCP(6)
        Port range: 1024 - 65535 (ephemeral port range)
        Source    : 0.0.0.0/0
        Allow/Deny: Allow
    
    outbound rules
 
        Rule #     : 110
        Type       : HTTP(80)
        Protocol   : TCP(6)
        Port range : 80
        Destination: 0.0.0.0/0
        Allow/Deny : Allow

        Rule #     : 120
        Type       : HTTPS(443)
        Protocol   : TCP(6)
        Port range : 443
        Destination: 0.0.0.0/0
        Allow/Deny : Allow

name tag: public2

associate public2 subnet with public2 nacl

Allow http traffic for updates
 
    inbound rules
 
        Rule #    : 100
        Type      : Custom TCP rule
        Protocol  : TCP(6)
        Port range: 1024 - 65535 (ephemeral port range)
        Source    : 0.0.0.0/0
        Allow/Deny: Allow
    
    outbound rules
 
        Rule #     : 100
        Type       : HTTP(80)
        Protocol   : TCP(6)
        Port range : 80
        Destination: 0.0.0.0/0
        Allow/Deny : Allow

        Rule #     : 110
        Type       : HTTPS(443)
        Protocol   : TCP(6)
        Port range : 443
        Destination: 0.0.0.0/0
        Allow/Deny : Allow


### Documentation

- [AWS VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)

    
    

    


    
    








