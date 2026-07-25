
# Encrypt Data with AWS KMS

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-kms)

**Author:** phadagi mannda raven  
**Email:** ecommercesraven@gmail.com

---

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-kms_w0x1y2z3)

---

## Introducing Today's Project!

In this project, I will demonstrate using encryption to secure  data.  The goal is to create encryption keys with with AWS KMS ( Key Management System ), encrypt  a DynamoDB tables's data with that key, then test access using IAM users. 

### Tools and concepts

Services I used include AWS KMS ( Key management services ) , DynamoD and AWS IAM. Key concepts I learnt include encryption, database tables , KMS using permission to actions rather than just access to he key itself; create a user to test access.

### Project reflection

This project took me approximately 1.5 hours inclung demo time  The most challenging part was undestanding how encryption works differently from other access controls tools.  It was most rewarding to see our test user get access to encryption . 

I chose to do this project today because  i wanted to arn all about encryption securing data and how it actually works. This project showed me the foundations of encryption keys and managing access as an admin.

---

## Encryption and KMS

Encryption is the process of turning Original data/plaintext data into secure format Companies and developers do this to secure their data from unautorized users.  Encryption keys are secure code that informs an algorithm on how it should encrypt.

AWS KMS is a vault for our encryption keys. Key management systems are important because they help us secure and manage the keys we sue to encrypt data. Unauthorized access to the key = exposing  our encrypted data which puts our security at risk. 

Encryption keys are broadly categorized as symmetric and asymmetric.  I set up a symmetric key because I will be using the same key  encrypt and decrypt our data.  Asymmetric key would be a Good choice if we need different keys for De/encryption 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-kms_a2b3c4d5)

---

## Encrypting Data

My encryption key will safeguard data in DynamoDB, which is safeguard data in DynamoDB, which is a fast and flexibe AWS data service. DynamoDB is great for application that need ast acces to large amounts of data e.g.  : GAMING

The different encryption options in DynamoDB include  DynamoDB ownd , AWS managed and customer managed ( CMK) . Their differences are based on who creates and  manages the key; and whether we have visibility. I selected the customer managed key option.


![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-kms_q8r9s0t1)

---

## Data Visibility

Rather than controlling who has access to the key, KMS manages user permissions by  controlling the actions that people can do with that key. In case, even if we gave our test the permission to see the key, it would need permission to decrypt 

Despite encrypting my DynamoDB table, I could still see the table's items because we are users of the key. DynamoDB uses transparent data encryption, which means it does the encryption / decryption process for us because it knows we're authorized 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-kms_c0d1e2f3)

---

## Denying Access

I configured a new IAM user to. validate whether  unauthorized users can have access  to encrypted data. The permission policies I granted this user are  DynamoDB full acesss but not encryption/decryption permission with AWS KMS. 

After accessing the DynamoDB table as the test user, I encountered an acess denie error message because our test user  has no acessto decrypion with the key . This confirmed that encryption key can used to secure data. 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-kms_w0x1y2z3)

---

## EXTRA: Granting Access

To let my test user use the encryption key, I ade it a key  user in the KMS console ! My key's policy was updated to allow the nextwork-kms-user to encrypt, decrypt re-encrypt using the key.

Using the test user, I retried accessing the DynamoDB table. I observed that the user can see the  data inside agin,  which confirmed that making it a key is an effctive way to authorize someone to see encryted data.

Encryption secures data instead of an entire resource or service.  I could combine encryption with other acess controltols like security groups and permission policies  tohave two layers of securty - the resource level, and then the data level. 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-kms_feffb2fb8)


## Defense in Depth

Encryption operates at the **data layer**, complementing — not replacing — resource-level controls such as:

- **Security Groups** (network layer)
- **IAM Policies** (identity layer)
- **VPC Endpoints** (network segmentation)
- **KMS Key Policies** (cryptographic layer)

By combining these controls, a multi-layered security posture is achieved: an attacker must compromise not only the resource access controls but also the encryption key to exfiltrate meaningful data.

---

## Infrastructure as Code

### Overview

The following sections provide declarative infrastructure definitions for reproducing this project using **AWS CloudFormation** and **HashiCorp Terraform**. Both templates provision:

- A symmetric customer-managed KMS key with an explicit key policy
- A DynamoDB table encrypted with the CMK
- An IAM test user with DynamoDB full access but no default KMS permissions
- A key policy update to demonstrate access restoration

---

### CloudFormation (YAML)

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: >
  DynamoDB table encrypted with a customer-managed KMS key,
  plus an IAM test user to validate KMS access control boundaries.

Parameters:
  TableName:
    Type: String
    Default: EncryptedDemoTable
  TestUserName:
    Type: String
    Default: nextwork-kms-user

Resources:
  # ------------------------------------------------------------------
  # KMS Customer-Managed Symmetric Key
  # ------------------------------------------------------------------
  DynamoDBEncryptionKey:
    Type: AWS::KMS::Key
    Properties:
      Description: CMK for DynamoDB server-side encryption
      EnableKeyRotation: true
      KeyPolicy:
        Version: "2012-10-17"
        Statement:
          # Allow the account root full control (required)
          - Sid: Enable IAM User Permissions
            Effect: Allow
            Principal:
              AWS: !Sub "arn:aws:iam::${AWS::AccountId}:root"
            Action: "kms:*"
            Resource: "*"
          # Allow the test user cryptographic actions
          - Sid: AllowTestUserCryptographicActions
            Effect: Allow
            Principal:
              AWS: !GetAtt TestUser.Arn
            Action:
              - kms:Encrypt
              - kms:Decrypt
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
              - kms:DescribeKey
            Resource: "*"
      Tags:
        - Key: Project
          Value: KMS-DynamoDB-Encryption

  DynamoDBEncryptionKeyAlias:
    Type: AWS::KMS::Alias
    Properties:
      AliasName: !Sub "alias/${TableName}-key"
      TargetKeyId: !Ref DynamoDBEncryptionKey

  # ------------------------------------------------------------------
  # DynamoDB Table with SSE using CMK
  # ------------------------------------------------------------------
  EncryptedTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: !Ref TableName
      BillingMode: PAY_PER_REQUEST
      AttributeDefinitions:
        - AttributeName: PK
          AttributeType: S
      KeySchema:
        - AttributeName: PK
          KeyType: HASH
      SSESpecification:
        SSEEnabled: true
        SSEType: KMS
        KMSMasterKeyId: !Ref DynamoDBEncryptionKey
      Tags:
        - Key: Project
          Value: KMS-DynamoDB-Encryption

  # ------------------------------------------------------------------
  # IAM Test User (no KMS permissions by default)
  # ------------------------------------------------------------------
  TestUser:
    Type: AWS::IAM::User
    Properties:
      UserName: !Ref TestUserName
      Tags:
        - Key: Project
          Value: KMS-DynamoDB-Encryption

  TestUserDynamoDBPolicy:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName: DynamoDBFullAccessPolicy
      Users:
        - !Ref TestUser
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Sid: DynamoDBFullAccess
            Effect: Allow
            Action: "dynamodb:*"
            Resource: "*"

Outputs:
  KeyArn:
    Description: ARN of the customer-managed KMS key
    Value: !GetAtt DynamoDBEncryptionKey.Arn
  TableArn:
    Description: ARN of the encrypted DynamoDB table
    Value: !GetAtt EncryptedTable.Arn
  TestUserArn:
    Description: ARN of the IAM test user
    Value: !GetAtt TestUser.Arn
```

**Deployment:**

```bash
aws cloudformation deploy \
  --template-file kms-dynamodb.yaml \
  --stack-name kms-dynamodb-stack \
  --capabilities CAPABILITY_NAMED_IAM
```

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

variable "table_name" {
  description = "Name of the DynamoDB table"
  type        = string
  default     = "EncryptedDemoTable"
}

variable "test_user_name" {
  description = "Name of the IAM test user"
  type        = string
  default     = "nextwork-kms-user"
}

# ------------------------------------------------------------------
# Data Sources
# ------------------------------------------------------------------
data "aws_caller_identity" "current" {}
data "aws_partition" "current" {}

# ------------------------------------------------------------------
# KMS Customer-Managed Symmetric Key
# ------------------------------------------------------------------
resource "aws_kms_key" "dynamodb_encryption_key" {
  description             = "CMK for DynamoDB server-side encryption"
  deletion_window_in_days = 30
  enable_key_rotation     = true

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid    = "Enable IAM User Permissions"
        Effect = "Allow"
        Principal = {
          AWS = "arn:${data.aws_partition.current.partition}:iam::${data.aws_caller_identity.current.account_id}:root"
        }
        Action   = "kms:*"
        Resource = "*"
      },
      {
        Sid    = "AllowTestUserCryptographicActions"
        Effect = "Allow"
        Principal = {
          AWS = aws_iam_user.test_user.arn
        }
        Action = [
          "kms:Encrypt",
          "kms:Decrypt",
          "kms:ReEncrypt*",
          "kms:GenerateDataKey*",
          "kms:DescribeKey"
        ]
        Resource = "*"
      }
    ]
  })

  tags = {
    Project = "KMS-DynamoDB-Encryption"
  }
}

resource "aws_kms_alias" "dynamodb_encryption_key_alias" {
  name          = "alias/${var.table_name}-key"
  target_key_id = aws_kms_key.dynamodb_encryption_key.key_id
}

# ------------------------------------------------------------------
# DynamoDB Table with SSE using CMK
# ------------------------------------------------------------------
resource "aws_dynamodb_table" "encrypted_table" {
  name         = var.table_name
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "PK"

  attribute {
    name = "PK"
    type = "S"
  }

  server_side_encryption {
    enabled     = true
    kms_key_arn = aws_kms_key.dynamodb_encryption_key.arn
  }

  tags = {
    Project = "KMS-DynamoDB-Encryption"
  }
}

# ------------------------------------------------------------------
# IAM Test User
# ------------------------------------------------------------------
resource "aws_iam_user" "test_user" {
  name = var.test_user_name
  tags = {
    Project = "KMS-DynamoDB-Encryption"
  }
}

resource "aws_iam_user_policy" "test_user_dynamodb_policy" {
  name = "DynamoDBFullAccessPolicy"
  user = aws_iam_user.test_user.name

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid      = "DynamoDBFullAccess"
        Effect   = "Allow"
        Action   = "dynamodb:*"
        Resource = "*"
      }
    ]
  })
}

# ------------------------------------------------------------------
# Outputs
# ------------------------------------------------------------------
output "key_arn" {
  description = "ARN of the customer-managed KMS key"
  value       = aws_kms_key.dynamodb_encryption_key.arn
}

output "table_arn" {
  description = "ARN of the encrypted DynamoDB table"
  value       = aws_dynamodb_table.encrypted_table.arn
}

output "test_user_arn" {
  description = "ARN of the IAM test user"
  value       = aws_iam_user.test_user.arn
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
| Create symmetric CMK | `AWS::KMS::Key` | `aws_kms_key` |
| Create key alias | `AWS::KMS::Alias` | `aws_kms_alias` |
| Create DynamoDB table with SSE | `AWS::DynamoDB::Table` | `aws_dynamodb_table` |
| Create IAM test user | `AWS::IAM::User` | `aws_iam_user` |
| Attach DynamoDB policy | `AWS::IAM::Policy` | `aws_iam_user_policy` |
| Update key policy for test user | Nested in `KeyPolicy` | Nested in `policy` argument |

---

## Summary

| Phase | Key Action | Outcome |
|-------|-----------|---------|
| Key Provisioning | Symmetric CMK created in KMS | Customer-controlled encryption key ready |
| Table Encryption | DynamoDB table configured with CMK SSE | Data encrypted at rest transparently |
| Access Denial | Test user with DynamoDB-only access | `AccessDeniedException` on KMS decrypt |
| Access Restoration | Test user added to CMK key policy | Successful plaintext data retrieval |
| IaC | CloudFormation + Terraform templates | Reproducible, version-controlled infrastructure |

---


