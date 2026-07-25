
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

---

---
