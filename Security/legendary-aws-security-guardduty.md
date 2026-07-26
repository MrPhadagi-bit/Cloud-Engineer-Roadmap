

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
