

# Threat Detection with GuardDuty

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-guardduty)

**Author:** phadagi mannda raven  
**Email:** ecommercesraven@gmail.com

---

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-guardduty_v1w2x3y4)

---

## Introducing Today's Project!

### Tools and concepts

The 

### Project reflection

---

## Project Setup

To set up for this project, we deployed a CloudFormation template that launches an insecure web app ( OWASP juice shop). The three main componets re the web app infrastructure, an S3 bucket and GuardDuty protecing our environment

The web app deployed is called OWASP juice shop. To practice our  GuardDuty skills, we will attack the jucie shop, and then visit the GuardDuty console to detect and analyze its findings , does it pick up on our attacks to our web app?

GuardDuty is an AI-powered threat detection service, which means it s designed to helps find and security attacks or vulnerabilities that affects our AWS resources/environment. Once it detects something unusual, it's up to us to investgate

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-guardduty_n1o2p3q4)

---

## SQL Injection

The first attack I performed on the web app is SQL injection, which means injecing malicious SQL code that manipulates a result from our web app. SQL injection is a security risk because it can let attackers bypass logins or  delete/ edit data

My SQL injection attack involved entering the code {' or 1=1;-- } into the email field of the web app's login page. This means the login query will always evaluate to true ( i.e. our database is manipulated into telling our web app this login exists).

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-guardduty_h1i2j3k4)

---

## Command Injection

Next, I used command injection which is a teachique that manipulates the web app's web server to run code that has been entered e.g in a form . The Juice Shop web app is vulnerable to this because it does not sanitize user inputs i.e does not block script.



![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-guardduty_t3u4v5w6)

---

## Attack Verification

To verify the attack's success, I visited the publicly exposed credentials file .This page showed me access keys that represents our EC2 instance's access to the developer's AWS environment.  Anyone can use those keys to get  the same level of access 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-guardduty_x7y8z9a0)

---

## Using CloudShell for Advanced Attacks

The attack continues in cloudshell, becaues this a tool we can use to run commmands that uses the credentials we've stole . CloudShell will be our medum for doing suspicious things like stealing data from an S3 bucket 

In cloudShell, I used "wget"  to download the exposed credentials fi into our CloudShell environment. Next, we ran a command using cat and jq to read the downloaded file and format it nicely so the credentials ( in JSON ) is easy are to understand.

I then set up a new profile that stores and save all of the stolen credentials. We had to create a new profile because the hacker doesn't inherently have access to the victims's AWS envirnment. I will need to use the profile to switch permission settings.

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-guardduty_j9k0l1m2)

---

## GuardDuty's Findings

After performing the attack, GuardDuty reported a finding within 16 minutes. Findings are notifications from GuardDuty hat something suspicious has happened, and they give you additioal  details about the who/what/when of the attack. 

GuardDuty's finding was called UnauthorizedAccess:IAMUser/InanceCredentialExfilrtration.InsideAWS, which means credentials belonging to my EC2 instance were being used in another account. Anomaly detection was used because this was unusual behaviour

GuardDuty's detailed finding reported that an S3 bucket was affected, the action that s done using the stolen credentials was GetObject; and thEC2 instance whose credentails were leaked The IP address + location of the actor was also available.

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-guardduty_v1w2x3y4)

---

## Extra: Malware Protection

For our project extension, I enabled Malware Protection for S3. Malware is file that containts threats e.g. opening the file will ause a data breach or deletion of resources.

To test Malware Protection, we uploaded n EICAR test file into a protected bucket. The uploaded file won't actually cause damae because the test file is only designed to alert antivirus software

Once we uploaded the malware, GuardDuty instanlty triggered a finding called Object:S3/MaliciousFile. This verified that  GuardDuty could successfully detect malare. it also meniond that the threat type is EICAR-Test-File ( which means not a virus ) 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-guardduty_sm42x3y4)

---

## Infrastructure as Code

### Overview

The following sections provide declarative infrastructure definitions for reproducing this GuardDuty lab using **AWS CloudFormation** and **HashiCorp Terraform**. Both templates provision:

- An Amazon GuardDuty detector (enabled by default)
- GuardDuty Malware Protection for S3
- An S3 bucket for exfiltration targets
- An IAM instance profile for the vulnerable EC2 application
- A VPC with public subnet for the OWASP Juice Shop deployment

> **Note:** The OWASP Juice Shop application itself is typically deployed via a pre-built CloudFormation template or container image. The snippets below provision the supporting AWS infrastructure and GuardDuty configuration.

---

### CloudFormation (YAML)

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: >
  GuardDuty threat detection lab: VPC, S3 bucket, IAM instance profile,
  GuardDuty detector, and Malware Protection for S3.

Parameters:
  LabPrefix:
    Type: String
    Default: guardduty-lab
    Description: Prefix for all resource names.
  VpcCidr:
    Type: String
    Default: 10.0.0.0/16
  PublicSubnetCidr:
    Type: String
    Default: 10.0.1.0/24

Resources:
  # ------------------------------------------------------------------
  # Networking
  # ------------------------------------------------------------------
  LabVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref VpcCidr
      EnableDnsHostnames: true
      EnableDnsSupport: true
      Tags:
        - Key: Name
          Value: !Sub "${LabPrefix}-vpc"

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref LabVPC
      CidrBlock: !Ref PublicSubnetCidr
      MapPublicIpOnLaunch: true
      AvailabilityZone: !Select [0, !GetAZs ""]
      Tags:
        - Key: Name
          Value: !Sub "${LabPrefix}-public-subnet"

  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: !Sub "${LabPrefix}-igw"

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref LabVPC
      InternetGatewayId: !Ref InternetGateway

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref LabVPC
      Tags:
        - Key: Name
          Value: !Sub "${LabPrefix}-public-rt"

  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn: AttachGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  SubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable

  # ------------------------------------------------------------------
  # S3 Bucket (Exfiltration Target)
  # ------------------------------------------------------------------
  ExfilBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub "${LabPrefix}-exfil-${AWS::AccountId}"
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: AES256
      VersioningConfiguration:
        Status: Enabled
      Tags:
        - Key: Project
          Value: GuardDuty-Lab

  # ------------------------------------------------------------------
  # IAM Instance Profile for Vulnerable EC2
  # ------------------------------------------------------------------
  JuiceShopInstanceRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: !Sub "${LabPrefix}-juiceshop-role"
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: ec2.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
      Tags:
        - Key: Project
          Value: GuardDuty-Lab

  JuiceShopInstanceProfile:
    Type: AWS::IAM::InstanceProfile
    Properties:
      InstanceProfileName: !Sub "${LabPrefix}-juiceshop-profile"
      Roles:
        - !Ref JuiceShopInstanceRole

  # ------------------------------------------------------------------
  # Security Group
  # ------------------------------------------------------------------
  JuiceShopSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupName: !Sub "${LabPrefix}-juiceshop-sg"
      GroupDescription: Allow HTTP and SSH
      VpcId: !Ref LabVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: !Sub "${LabPrefix}-juiceshop-sg"

  # ------------------------------------------------------------------
  # GuardDuty Detector
  # ------------------------------------------------------------------
  GuardDutyDetector:
    Type: AWS::GuardDuty::Detector
    Properties:
      Enable: true
      FindingPublishingFrequency: FIFTEEN_MINUTES
      DataSources:
        S3Logs:
          Enable: true
        Kubernetes:
          AuditLogs:
            Enable: false
        MalwareProtection:
          ScanEc2InstanceWithFindings:
            Enable: true

  # ------------------------------------------------------------------
  # GuardDuty Malware Protection for S3
  # ------------------------------------------------------------------
  GuardDutyMalwareProtectionPlan:
    Type: AWS::GuardDuty::MalwareProtectionPlan
    Properties:
      Role: !GetAtt GuardDutyMalwareProtectionRole.Arn
      ProtectedResource:
        S3Bucket:
          BucketName: !Ref ExfilBucket
      Actions:
        - Tagging
      Tags:
        - Key: Project
          Value: GuardDuty-Lab

  GuardDutyMalwareProtectionRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: !Sub "${LabPrefix}-guardduty-malware-role"
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: malware-protection-plan.guardduty.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonGuardDutyMalwareProtectionPolicy
      Tags:
        - Key: Project
          Value: GuardDuty-Lab

Outputs:
  VpcId:
    Description: Lab VPC ID
    Value: !Ref LabVPC
  PublicSubnetId:
    Description: Public subnet ID
    Value: !Ref PublicSubnet
  ExfilBucketName:
    Description: S3 bucket for exfiltration testing
    Value: !Ref ExfilBucket
  InstanceProfileArn:
    Description: IAM instance profile for Juice Shop EC2
    Value: !GetAtt JuiceShopInstanceProfile.Arn
  GuardDutyDetectorId:
    Description: GuardDuty detector ID
    Value: !Ref GuardDutyDetector
```

**Deployment:**

```bash
aws cloudformation deploy \
  --template-file guardduty-lab.yaml \
  --stack-name guardduty-lab-stack \
  --capabilities CAPABILITY_NAMED_IAM
```

---

---

### Terraform (HCL)

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

# ------------------------------------------------------------------
# Variables
# ------------------------------------------------------------------
variable "aws_region" {
  description = "AWS region for resource deployment"
  type        = string
  default     = "us-east-1"
}

variable "lab_prefix" {
  description = "Prefix for all resource names"
  type        = string
  default     = "guardduty-lab"
}

variable "vpc_cidr" {
  description = "CIDR block for the lab VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "public_subnet_cidr" {
  description = "CIDR block for the public subnet"
  type        = string
  default     = "10.0.1.0/24"
}

# ------------------------------------------------------------------
# Data Sources
# ------------------------------------------------------------------
data "aws_caller_identity" "current" {}
data "aws_partition" "current" {}
data "aws_availability_zones" "available" {
  state = "available"
}

# ------------------------------------------------------------------
# Networking
# ------------------------------------------------------------------
resource "aws_vpc" "lab_vpc" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "${var.lab_prefix}-vpc"
  }
}

resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.lab_vpc.id
  cidr_block              = var.public_subnet_cidr
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[0]

  tags = {
    Name = "${var.lab_prefix}-public-subnet"
  }
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.lab_vpc.id

  tags = {
    Name = "${var.lab_prefix}-igw"
  }
}

resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.lab_vpc.id

  tags = {
    Name = "${var.lab_prefix}-public-rt"
  }
}

resource "aws_route" "public_internet" {
  route_table_id         = aws_route_table.public_rt.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = aws_internet_gateway.igw.id
}

resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.public_rt.id
}

# ------------------------------------------------------------------
# S3 Bucket (Exfiltration Target)
# ------------------------------------------------------------------
resource "aws_s3_bucket" "exfil_bucket" {
  bucket = "${var.lab_prefix}-exfil-${data.aws_caller_identity.current.account_id}"

  tags = {
    Project = "GuardDuty-Lab"
  }
}

resource "aws_s3_bucket_public_access_block" "exfil_bucket_pab" {
  bucket = aws_s3_bucket.exfil_bucket.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

resource "aws_s3_bucket_server_side_encryption_configuration" "exfil_bucket_sse" {
  bucket = aws_s3_bucket.exfil_bucket.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}

resource "aws_s3_bucket_versioning" "exfil_bucket_versioning" {
  bucket = aws_s3_bucket.exfil_bucket.id
  versioning_configuration {
    status = "Enabled"
  }
}

# ------------------------------------------------------------------
# IAM Instance Profile for Vulnerable EC2
# ------------------------------------------------------------------
resource "aws_iam_role" "juiceshop_role" {
  name = "${var.lab_prefix}-juiceshop-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
        Action = "sts:AssumeRole"
      }
    ]
  })

  managed_policy_arns = [
    "arn:${data.aws_partition.current.partition}:iam::aws:policy/AmazonS3ReadOnlyAccess"
  ]

  tags = {
    Project = "GuardDuty-Lab"
  }
}

resource "aws_iam_instance_profile" "juiceshop_profile" {
  name = "${var.lab_prefix}-juiceshop-profile"
  role = aws_iam_role.juiceshop_role.name
}

# ------------------------------------------------------------------
# Security Group
# ------------------------------------------------------------------
resource "aws_security_group" "juiceshop_sg" {
  name        = "${var.lab_prefix}-juiceshop-sg"
  description = "Allow HTTP and SSH"
  vpc_id      = aws_vpc.lab_vpc.id

  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "${var.lab_prefix}-juiceshop-sg"
  }
}

# ------------------------------------------------------------------
# GuardDuty Detector
# ------------------------------------------------------------------
resource "aws_guardduty_detector" "main" {
  enable                       = true
  finding_publishing_frequency = "FIFTEEN_MINUTES"

  datasources {
    s3_logs {
      enable = true
    }
    kubernetes {
      audit_logs {
        enable = false
      }
    }
    malware_protection {
      scan_ec2_instance_with_findings {
        enable = true
      }
    }
  }
}

# ------------------------------------------------------------------
# GuardDuty Malware Protection for S3
# ------------------------------------------------------------------
resource "aws_iam_role" "guardduty_malware_role" {
  name = "${var.lab_prefix}-guardduty-malware-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Principal = {
          Service = "malware-protection-plan.guardduty.amazonaws.com"
        }
        Action = "sts:AssumeRole"
      }
    ]
  })

  managed_policy_arns = [
    "arn:${data.aws_partition.current.partition}:iam::aws:policy/AmazonGuardDutyMalwareProtectionPolicy"
  ]

  tags = {
    Project = "GuardDuty-Lab"
  }
}

resource "aws_guardduty_malware_protection_plan" "s3_protection" {
  role = aws_iam_role.guardduty_malware_role.arn

  protected_resource {
    s3_bucket {
      bucket_name = aws_s3_bucket.exfil_bucket.bucket
    }
  }

  actions {
    tagging {
      status = "ENABLED"
    }
  }
}

# ------------------------------------------------------------------
# Outputs
# ------------------------------------------------------------------
output "vpc_id" {
  description = "Lab VPC ID"
  value       = aws_vpc.lab_vpc.id
}

output "public_subnet_id" {
  description = "Public subnet ID"
  value       = aws_subnet.public_subnet.id
}

output "exfil_bucket_name" {
  description = "S3 bucket for exfiltration testing"
  value       = aws_s3_bucket.exfil_bucket.bucket
}

output "instance_profile_arn" {
  description = "IAM instance profile for Juice Shop EC2"
  value       = aws_iam_instance_profile.juiceshop_profile.arn
}

output "guardduty_detector_id" {
  description = "GuardDuty detector ID"
  value       = aws_guardduty_detector.main.id
}
```

**Deployment:**

```bash
terraform init
terraform plan
terraform apply
```

---

## IaC Resource Mapping

| Manual Console Step | CloudFormation Resource | Terraform Resource |
|---------------------|------------------------|--------------------|
| Create VPC | `AWS::EC2::VPC` | `aws_vpc` |
| Create public subnet | `AWS::EC2::Subnet` | `aws_subnet` |
| Create internet gateway | `AWS::EC2::InternetGateway` | `aws_internet_gateway` |
| Configure route table | `AWS::EC2::RouteTable` / `Route` | `aws_route_table` / `aws_route` |
| Create S3 bucket | `AWS::S3::Bucket` | `aws_s3_bucket` |
| Enable S3 SSE | `BucketEncryption` (nested) | `aws_s3_bucket_server_side_encryption_configuration` |
| Create IAM role for EC2 | `AWS::IAM::Role` | `aws_iam_role` |
| Create instance profile | `AWS::IAM::InstanceProfile` | `aws_iam_instance_profile` |
| Create security group | `AWS::EC2::SecurityGroup` | `aws_security_group` |
| Enable GuardDuty detector | `AWS::GuardDuty::Detector` | `aws_guardduty_detector` |
| Enable Malware Protection for S3 | `AWS::GuardDuty::MalwareProtectionPlan` | `aws_guardduty_malware_protection_plan` |
| Create IAM role for malware scanning | `AWS::IAM::Role` | `aws_iam_role` |

---

## Kill Chain Summary

| Phase | Tactic | Technique | GuardDuty Coverage |
|-------|--------|-----------|-------------------|
| Initial Access | Exploit Public-Facing App | T1190 — SQL Injection | Indirect (via CloudTrail anomaly) |
| Execution | Command and Scripting Interpreter | T1059 — Command Injection | Indirect (via CloudTrail anomaly) |
| Credential Access | Unsecured Credentials | T1552 — Credentials in Files | `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS` |
| Exfiltration | Exfiltration Over Web Service | T1567 — Exfiltration to Cloud Storage | `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS` |
| Impact | Data Encrypted for Impact | N/A (test vector) | `Object:S3/MaliciousFile` |

---

## Summary

| Phase | Key Action | Outcome |
|-------|-----------|---------|
| Lab Setup | CloudFormation deployment of vulnerable app + GuardDuty | Functional threat detection environment |
| Attack Execution | SQLi → Command Injection → Credential Exfiltration | Successful compromise and lateral movement |
| Detection | GuardDuty finding generation (~16 min) | High-severity credential exfiltration finding surfaced |
| Extension | Malware Protection for S3 + EICAR test | `Object:S3/MaliciousFile` finding validated |
| IaC | CloudFormation + Terraform templates | Reproducible, version-controlled lab infrastructure |

---

