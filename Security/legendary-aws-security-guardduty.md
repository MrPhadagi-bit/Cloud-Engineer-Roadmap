

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

