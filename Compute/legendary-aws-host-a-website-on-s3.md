# Host a Website on Amazon S3

> **A hands-on project demonstrating how to host a static website using Amazon S3, including bucket configuration, static website hosting, ACL management, and bucket policies.**


**Project Link:** [View Project](http://nextwork.ai/projects/aws-host-a-website-on-s3)

**Author:** phadagi mannda raven  
**Email:** ecommercesraven@gmail.com

---

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-host-a-website-on-s3_5d4474f9)

---

## Introducing Today's Project!

### Project overview

In this project, I will demonstrate... I'm doing this project to learn...host a static websit on s3

### Tools and concepts

Services I used were... Key concepts I learnt include...

### Time, challenges, and wins

---

## How I Set Up an S3 Bucket

### What I did in this step

In this step, I will open up S3 and then create as storage space inside to start storing website files 

### How long it took to create the bucket

Creating an S3 bucket took me less then 5 minutes- we needed to learn a few new concepts like block public access and ACLs, but once that learning is done, we can create buckets in even shorter time in future 

### Region selection

The Region I picked for my S3 bucket was cape town , because it's best practice to pick the regon closest to me. It's best practice to pick the region closest to you because it lowers time to retrieve you things [ aka latency ] , and cost .. 

### Understanding bucket name uniqueness

S3 bucket names are globally unique! This means no two amazon s3 buckets in the entire wold can have the same name. They have to be unique, regardless of the region the account ID.

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-host-a-website-on-s3_ba6d42ad)

---

## Upload Website Files to S3

### What I did in this step

In this step, I will uupload the website files into our s3 bucket. This is important because no files = no website ! Our bucket is still empty , let  e get files inside so that we have a website 

### Files I uploaded

I uploaded two files to my S3 bucket - they were an index.html file [ this determine the structure i.e. what goes inside your website ] and  folderof images and assets [ his will fill in the website with  images and things to look at ] 

### How the files work together

Both files are necessary for this project as index.html determines the structure , but the structure alone does not illustrate the cotents of the website. i.e if index.html say 
"nsert image here", t might not have he actual image to display - you'd need to supply that image separately.that' why we have multiple files uploaded - the index.html file to diret what is inside the websitepage , but also a bunch of assests [ like images ] that the website is trying to display.

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-host-a-website-on-s3_a265af88)

---

## Static Website Hosting on S3

### What I did in this step

In this step, I will make our website available to the world! This is called static  website hosting, and it's important because our  websitefiles will stay as "just files" and not turn into a website  we don't this 

### Understanding website hosting

Website hosting means putting our website files on a web server, which is a speial computer designed to turn the files  into a website that people can vist  

### How I enabled website hosting

To enable website hosting with my S3 bucket, I went to the Properties tab of our bucket, enabled static website hosting and we also labelled "index.html" as our ndex document i.e  this s the document that we're trying to host.

### Access Control Lists (ACLs)

An ACL is  a way to configure permission ettings inside a bucket. I   enabled ACLs so that we can control access to our website files later. There was a popup mentioning that AWS recommends disabling ACLs, but keeep it enbled to learn how ACLs workand compare t with bucket policies 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-host-a-website-on-s3_c22c54c0)

---

## Bucket Endpoints

### Understanding bucket endpoint URLs

Once static website is enabled, S3 produces a bucket endpoint URL, which is  a URL that takes you [anyone on the internet ]to the website that you're hostng.

### What I saw when I tested the endpoint

When I first visited the bucket endpoint URL, I saw 403 forbidden error! The reason for this error was objects in  bucket are public by default - even though we've though weve switched off "Block all Public Acess" , the website files themselve are still completely private. We need to manage thier access settings separately  they need to be public files for the public to see the contents of our website

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-host-a-website-on-s3_22ce4daf)

---

## Success!

### What I did in this step

In this step, I will fgo to the files i have uploaded into ur uket and make them public too because this will enable us toa actually view the contents of our website! Once that's done , our website is officilly live  

### How I resolved the 403 error

To resolve this 403 Forbidden error, I updated the files inside our bucket too Using ACLs we made our buckets files publice!, Once i checked my s3 bucket endoint i ca see a webpage all loaded up 

![Image](http://nextwork.ai/gleeful_navy_kind_rabbit/uploads/aws-host-a-website-on-s3_5d4474f9)

---

## Bucket Policies

### What I did in this extension

### Understanding bucket policies

### What my bucket policy does

---

---
