# Cloud Security with AWS IAM

> **A hands-on project demonstrating AWS IAM security best practices — including policy authoring, user group management, least-privilege access control, and the IAM Policy Simulator.**

---

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-iam)

**Author:** phadagi mannda raven  
**Email:** ecommercesraven@gmail.com

---

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-iam_1c864649)

---

## Introducing Today's Project!

### Project overview

In this project, I will demonstrate...We'll launch an EC2 instance, then control who has access to it by creating some IAM policies and user groups I'm doing this project to learn...Cloud Security with AWS IAM

### Tools and concepts

Services I used were EC2 and IAM , Key concepts I learnt include IAM user , policies user group and account allases. We also learn how to use the policy simulator and how JSON  policy work. How to launch an instance, how to tag an instance, how log in as another user.

### Project reflection

This project took me approximately 2 hours including project demo time! The most challenging part was understanding  the IAM policy since it was written in JSON and it contained multiple statements. It was most rewarding to see permission denied when our intern tried delete our prodution instance MY IAM access managemnt  policy is working

---

## Tags

### What I did in this step

In this step, I will launch an EC2 instance 

### Understanding tags

Tags are organisational tools that letsus  label our resources. They are helpful for grouping resources, cost allocation and applying policies for all resources with the same tags.

### My tag configuration

The tag I’ve used on my EC2 instances is called Env, which stands for environment. The value I’ve assigned for our  instances are production and development!

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-iam_2e0e5a5d)

---

## IAM Policies

### What I did in this step

In this step, We will use IAM policies to control the access level of a new NextWork intern  because they should have access to the development environment { i.e the development instance } but not the production environment

### Understanding IAM policies

IAM Policies are like rules that determine who can do what in our aws account. We're using policis today to control who has policies today to control who has access to our production/environment instance. 

### The policy I set up

For this project, I’ve set up a policy using JSON 

### Policy effect

I’ve created a policy that allows the policy holder (i.e. the intern ) to have permission to do anything they want to any instance taggd with  " development ". They can see information for any instance, but they're denied access to deleting/creating tags for any instance.

### Understanding Effect, Action, and Resource

The Effect, Action, and Resource attributes of a JSON policy means whether or not the policy is allowing /denying action ( i.e.Effect ) ; what the policy hold can or cannot do ( i.e Action); and the specific AWS rsources that the policy  relates to ( i.e. Resource ).

---

## My JSON Policy

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-iam_1c864649)

---

## Account Alias

### What I did in this step

In this step, we will setup an Account Alias, which is like a nickname for our AWS account's consol login. This is because an account alias makes it simpler for our users to login!

### Understanding account aliases

An account alias is simply a nickname for our aws account! instead of a long account ID , We can now reference our account alias instead!

### Setting up my account alias

Creating an account alias took me 30 seconds - it'sa simple configuration in the IAM dashboard. Now, my new AWS console sign-in URL uses the alies instead of my account ID

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-iam_0eb4439b)

---

## IAM Users and User Groups

### What I did in this step

In this step,  We will sey up two IAM resources - IAM users, and IAM user groups . This is  because IAM  users ar like logins for people that want access to our AWS account, while user group are like folders to manage users that have the same level of access. 

### Understanding user groups

IAM user groups are like folders that collect IAM users so that you can apply permission settings at the group level. 

### Attaching policies to user groups

We attached the policy we created to this user group, which means any user group, which means any users created inside this group will automatically gt the permissions attachd to our stackflow-nextwork-dev-group will automatically get the permissions attachedto our stackflow-nextwork-dev-group policy 



### Understanding IAM users

IAM users are people or entities that have access/can login to our AWS account. 

---

## Logging in as an IAM User

### Sharing sign-in details

The first way is to email sign-in instructions to the user, whil the seond way is to  download a csv file with  the sign in details inside.

### Observations from the IAM user dashboard

Once we logged in as our IAM user, We noticed that our user is already denied access to panels on the main AWS cosole dashboard.  This was because we only set up permissions to our development EC2 instance, so our intern wouldn't have access to even see anything else.

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-iam_6f2ab446)

---

## Testing IAM Policies

### What I did in this step

In this step,  we will login to our own AWS account as the intern and test access  to the production and development instances because we want to make our intern doesn't have ability to do anything that can affect our production environment.

### Testing policy actions

We tested our JSON IAM policy by attempting to stop both the development and production instance

### Stopping the production instance

When w  tried to stop the production instance, we were met with an error!  This was because our production instance is tagged with "production" label, which is out side of our prmission policy - intern are only allowed to do things to the development instances. 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-iam_0e7a9d6a)

### Stopping the development instance

Next, when we tried to stop the development instance, we succesfully saw the instance state  change to Stopping and then stopped. This was because our permission policy allows the intern { i.e users in the nextwork-dev-stackflow- group } to stop instances , 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-iam_1811801c)

---

## IAM Policy Simulator

To extend my project, I'm going to test our permission policies in safer and controlled way- a tool called the IAM Policy Simulator ! I'm doing this because having to stop instances and log into AWS account as other users is a bit disruptive. Lets find a more efficient way.

### Understanding the IAM Policy Simulator

The IAM Policy Simulator is a tool that lets us simulate actions and test permission settings by defining a specific user/group/role and the action we want to test for . It's useful for saving time when testing permission settings , no more logging into another user or actually stopping resources

### How I used the simulator

I set up a simulation for wheather our dev user group has permission to stop instances or delete tags. The results were denied for both we had to adjust the scope of the EC2 instancs to ones that are tagged with  "development" . Once we applied that tag, permission was allowed.

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-security-iam_069d8a621)

---

## Challenges Faced & Solutions

| Challenge | Solution |
|---|---|
| **Complex JSON policy syntax** | Broke down the policy into Effect/Action/Resource components; used visual formatting to understand structure |
| **Multiple policy statements** | Learned that IAM policies can contain multiple statements evaluated independently |
| **Testing without disruption** | Used the IAM Policy Simulator for safe, iterative policy validation |
| **Understanding deny vs. allow precedence** | Confirmed that explicit Deny always overrides Allow — critical for security boundaries |

---

## Key Takeaways

1. **Least Privilege Principle** — Grant only the permissions required for a specific task. The intern can manage development instances but cannot touch production.

2. **Tags as Security Boundaries** — Resource tags aren't just for organization; they can drive fine-grained access control at scale.

3. **Policy Precedence** — `Deny` > `Allow` > Implicit Deny. Design policies with explicit denies for critical guardrails.

4. **Group-Based Management** — Attach policies to groups, not individual users. It scales and simplifies permission audits.

5. **Test Before Deploy** — The IAM Policy Simulator prevents production incidents by validating policies in a safe environment.

6. **Account Aliases Improve UX** — A simple 30-second configuration makes sign-in significantly easier for teams.

---

## Project Status

| Component | Status |
|---|---|
| EC2 Instance Launch & Tagging |  Complete |
| JSON IAM Policy Authoring |  Complete |
| Account Alias Configuration |  Complete |
| IAM User & Group Creation |  Complete |
| Live Permission Testing (Production Denied) |  Complete |
| Live Permission Testing (Development Allowed) |  Complete |
| IAM Policy Simulator Validation |  Complete |

