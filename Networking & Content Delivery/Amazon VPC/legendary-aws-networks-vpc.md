# Build a Virtual Private Cloud (VPC)

> **A hands-on project demonstrating AWS VPC architecture — including CIDR block design, public/private subnet segmentation, Internet Gateway configuration, and AWS CLI deployment.**


**Project Link:** [View Project](http://nextwork.ai/projects/aws-networks-vpc)

**Author:** phadagi mannda raven  
**Email:** ecommercesraven@gmail.com

---

## Build a Virtual Private Cloud (VPC)

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-networks-vpc_2facf927)

---

## Introducing Today's Project!

In this project, I will demonstrate how to create virtual private cloud . I'm doing this project to learn aws cloud [ vpc , public subnet and internet gateways 

### What is Amazon VPC?

### Personal reflection

---

## Virtual Private Clouds (VPCs)

### What I did in this step

In this step, I will access the vpc console in aws and create a virtual  private cloud . 

### How VPCs work

VPCs are virtual private cloud which act create private access to you cloud resource,

### Why there is a default VPC in AWS accounts

There was already a default VPC in my account ever since my AWS account was created. This is because most AWS resource like EC2 need VPC to access the internet gate or create on, without it would be impossible 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-networks-vpc_2facf927)

### Defining IPv4 CIDR blocks

To set up my VPC, I had to define an IPv4 CIDR block, which is IPv4 stands for Internet Protocol version 4, which is the most common way to write an IP address and CIDR  (which stands for Classless Inter-Domain Routing) is a way to assign a whole block of IP addresses, kind of like creating a zone/area in a city.

---

## Subnets

### What I did in this step

In this step, I will public Subnet, think of a subnet as your next step is to divide this large space into subdivisions called subnets, so you can start planning where different resources will live and operate.



### Creating and configuring subnets

Subnets are like little regions in our VPC called ''availability zone''. you use subnets to group resources with similar access rules and restrictions. There two types of Subnets public and private. There are already subnets existing in my account, one for every Availability  Zone in Your Region.

### Public vs private subnets

The difference between public and private subnets are public subnets have direct access to  internet [ Resources inside a public subnet can communicate with external networks ] and Private Subnets need a internet gateway [You'd use it for internal resources that don’t need to be publicly accessible]. 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-networks-vpc_157c4219)

### Auto-assigning public IPv4 addresses

Once I created my subnet, I enabled public IPv4. This setting makes sure allows internal communication within your VPC. so that access the internet or be accessible from the internet, the instance would need a public IP address.

---

## Internet gateways

### What I did in this step

In this step, I will create an internet gateway because is like building a bridge (internet gateway) that links your private city (VPC) to the outside world (the internet), so your resources can communicate beyond your private space.

### Setting up internet gateways

Internet gateways are key to making applications available on the internet. By attaching an internet gateway, your instances can access the internet and be accessible to external users. 

Attaching an internet gateway to a VPC means  resources in your VPC can now access the internet. The EC2 instances with public IP addresses also become accessible to users, so your applications hosted on those servers become public too. If I missed this step I would have no internet access. 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-networks-vpc_4ae90410)

---

## Using the AWS CLI

### What I'm doing in this extension

In this project extension, I will to use the AWS CLI to launch your VPC's resources... and report back on whether it was a faster, more efficient way to do this project

### Exploring CloudShell and CLI

VPC resources could also be created with CloudShell, which is... CLI is (Command Line Interface) is a software that lets you create, delete and update AWS resources with commands instead of clicking through your console.

### Debugging my setup

To set up a VPC or a subnet, you can use the command... Make sure to avoid errors by including 

### Comparing CloudShell vs AWS Console

---

---
