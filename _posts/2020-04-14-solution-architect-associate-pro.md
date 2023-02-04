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
mathjax: true
mathjax_autoNumber: true
---


## A.1-Definition

**AWS CloudFront** is a **content delivery network** who improve the delivery of content from it's original location to the viewer of that data **by caching** and using AWS global networkµ

## A.2-Some Keys Terms

|    |      |
|:--------------- |:--------  |
| `Origin`        |  Source location of your content (S3 or custom origin). For one CloudFornt configuration, <br>we can have **one or more origin**it  |
| `Edge Location`  | Local cache of your data(not to deploy an EC2 instance) |
| `Distribution`  | Configuration unit of CloudFront. A **distribution** can have many **behavior** which are <br> configured with a path pattern. If request match that pattern, that behavior is used <br> otherwise the default. <br> It's like : [xxx.cloudfront.net](xxx.cloudfront.net) |
| `Edge Location`  | Local cache of your data(not to deploy an EC2 instance) |
| `Regional Edge Cache`  | Larger version of an **Edge location** and support a number of **edge location** in the same <br> geographical location. <br> > Only use **regional edge cache** if the content origin is not S3.|
| `Cache hit`  | Delivering object from the closest **edge location** with low latency. **Edge location** might <br> check it's closest **regional cache**. |
| `Cache miss`  | Delivering object that is not in the closest **edge location**. |
| `Cache behavior`  | Locaye between **origin** and **distribution**. |
| `Trusted signers`  | Accounts that are generally able to generate **signed URL** or **signed cookies**. (refer to a <br> restrictb access to a behavior  nd to the content deliver by that behavior). <br> > We can aqociate a **lambda@edge** function at the behavior level. |
| `Price Class`  | Determine which **edge locations** your distribution is deployed to. |

The are two important architectural things to note here:

* **AWS CloudFront** is integrates whith **AWS Certificate Manager ACM**, so you can use **SSL Certificates** with **CloudFront**

* **CloudFront** is for download-style operations only. **Any upload goes direct to the origin for processing**.
  > **CloudFront perform no write caching**

| At the **distribution level**, <br>we can configure   | At the **distribution level**,<br> we can configure:     |
|:--------------- |:--------  |
| `Description`        | `Path pattern`     |
| `Supported HTTP version`  | `Origin and origin group`  |
| `Alternate domain name`  | `Viewer Protocol policies`  |
| `Custom SSL Certificate`  | `Encrypt` data from the point it enters the edge location through the CloudFront <br>network  |
| `Security policy`  | Set all the `cash directives` within a behavior by using `cache settngs`(old one) <br> or using `cache policy*` and `origin request policy settings` |
| `Standard/ Cookie logging`  | `Path pattern`  |
| `Defaukt root object`  | The minimum, maximum and default `TTL`  |
| `Price Class`  | You are alse able to restrict Viewer access to a behavior  |

## A.3 CloudFront and TTL

|    |
|:--------------- |
| More frequent `cache Hits`  =   `Lower origin load` |
| Default TTL (`Behavior`)   =    `24 hours` (validity period) |
| You can set `Minimum TTL` and  `Maximum TTL` |
| Origin header: `Cache-Control max-age`(seconds) |
| Origin header: `Cache-Control s-maxage`(seconds)|
| Origin header `Expires(Date & Time)`      |
| Cache invalidation perfored on a `distribution` applies to all edge locations and `it takes time`      |

### A.4 CloudFront - SSL & SNI
  * Each`CloudFront` distribution, receives a default domain name when it's created, it's a `CNAME` (default name to access the distribution: http://xxx.cloudfront.net/).
  * SSL supported by default `*cloudfront.net` cert.
  * For **global services**, like **CloudFront**, certificates need tp be created in `us-east-1`
  *  If you choose to use **HTTPS** with **CloudFront**, then **you have to have the appropriate certificates which match the name that you are using for that distribution**

The are actually two set of connections :
  * The connections between the **Viewer ==> CloudFront edge location** 
  * The connections between the **CmoudFornt ==> origin been used**

> These connections are knows as **viewer and origin protocols** and both **of thoses connections need a valid public certificates** e.g **self-signed certificates wil not work with CloudFront**

A **SNI (Server Name Indication)** is a **TLS extension allowing a host to be included**, it's add the ability for a client to tell a server which domain name it's attempting to access. And this occurs within the TLS handshake.

When you are establishing the encrypted connection between your device and [amazon.com](amazon.com):

- Your devise can tell the server that it wants to accexx [Amazon.com](amazon.com).

* The server can then rspond with the [Amazon.com](amazon.com) certificate proving its identity as an [Amazon.com](amazon.com) server. And this allows one IP to host many `HTTPS` websites which need their own certificates.

> The problem is that all the browsers don't support `SNI`.
> 
> If you want to use `CloudFront` and you want to allow `HTTPS` connections, and you want to use custom certificate with the custom domain name, the `CloudFront` **need to provide dedicated IP addresses if he need to support older browsers**
>
> So when using `CloudFront`, 
> >* **You can either choose to use** `SNI` mode which is free as part of the service
> >* **You choose to use a dedicated IP** at the edge location and this cots money

| AttributeName   | key     |
|:--------------- |:--------  |
| ${\color{blue}docker \space rename}$        | Creates a container but does not start it     |

C:\Users\thier\OneDrive\Documents\GitHub\oliana2\assets\img\others

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



Given an gray-scale image:

$$ \begin{bmatrix}
    3 & 0 & 1 & 2 & 7 & 4 \\
    1 & 5 & 8 & 9 & 3 & 1 \\
    2 & 7 & 2 & 5 & 1 & 3 \\
    0 & 1 & 3 & 1 & 7 & 8 \\
    4 & 2 & 1 & 6 & 2 & 8 \\
    2 & 4 & 5 & 2 & 3 & 9 \\
\end{bmatrix} $$
