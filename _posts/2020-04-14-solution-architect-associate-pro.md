---
description: >-
  Zweilosec's writeup on the xxx-difficulty xxx machine xxx from 
  https://hackthebox.eu
title: CloudFront                   # Add title of the machine here
date: 2023-02-02 08:00:00 -0600                           # Change the date to match completion date
categories: [AWS Certification Cheatsheet, AWS Certified Solutions Architect – Professional]                     # Change Templates to Writeup
tags: [AWS, Certification, CloudFront, template]     # TAG names should always be lowercase; replace template with writeup, and add relevant tags
show_image_post: false                                    # Change this to true
#image: /assets/img/machine-0-infocard.png                # Add infocard image here for post preview image
---


## A.1-Definition

**AWS CloudFront** is a **content delivery network** who improve the delivery of content from it's original location to the viewer of that data **by caching** and using AWS global networkµ

## A.2-Some Keys Terms

|    |      |
|:--------------- |:--------  |
| `Origin`        |  Source location of your content (S3 or custom origin). For one CloudFornt configuration, <br>we can have **one or more origin**it  |
| `Edge Location`  | Local cache of your data(not to deploy an EC2 instance) |
| `Distribution`  | Configuration unit of CloudFront. A **distribution** can have many **behavior** which aere configured with a path pattern. If request match that pattern, that behavior is used otherwise the default. |
| `Edge Location`  | Local cache of your data(not to deploy an EC2 instance) |
| `Regional Edge Cache`  | Larger version of an **Edge location** and support a number of **edge location** in the same geographical location. |
|   | Only use **regional edge cache** if the content origin is not S3. |
| `Cache hit`  | Delivering object from the closest **edge location** with low latency. **Edge location** might check it's closest **regional cache**. |
| `Cache miss`  | Delivering object that is not in the closest **edge location**. |
| `Cache behavior`  | Locaye between **origin** and **distribution**. |

The are two important architectural things to note here:

* **AWS CloudFront** is integrates whith **AWS Certificate Manager ACM**, so you can use **SSL Certificates** with **CloudFront**

* **CloudFront** is for download-style operations only. **Any upload goes direct to the origin for processing**.
  > **CloudFront perform no write caching**

Short description to include any strange things to be dealt with

## Useful Skills and Tools

### Useful thing 1

description with generic example

### Useful thing 2

description with generic example

## Enumeration

### Nmap scan

I started my enumeration with an nmap scan of `10.10.10.xxx`.  The options I regularly use are:

| `Flag` | Purpose |
| :--- | :--- |
| `-p-` | A shortcut which tells nmap to scan all ports |
| `-vvv` | Gives very verbose output so I can see the results as they are found, and also includes some information not normally shown |
| `-sC` | Equivalent to `--script=default` and runs a collection of nmap enumeration scripts against the target |
| `-sV` | Does a service version scan |
| `-oA $name` | Saves all three formats \(standard, greppable, and XML\) of output with a filename of `$name` |

## Initial Foothold

## Road to User

### Further enumeration

### Finding user creds

### User.txt

## Path to Power \(Gaining Administrator Access\)

### Enumeration as user `username`

### Getting a shell

### Root.txt

Thanks to [`<box_creator>`](https://www.hackthebox.eu/home/users/profile/<profile_num>) for something interesting or useful about this machine.

If you have comments, issues, or other feedback, or have any other fun or useful tips or tricks to share, feel free to contact me on Github at [https://github.com/zweilosec](https://github.com/zweilosec) or in the comments below!

If you like this content and would like to see more, please consider buying me a coffee! <a href="https://www.buymeacoffee.com/zweilosec"><img src="https://img.buymeacoffee.com/button-api/?text=Buy me a coffee&emoji=&slug=zweilosec&button_colour=FFDD00&font_colour=000000&font_family=Lato&outline_colour=000000&coffee_colour=ffffff"></a>
