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
usemathjax: true
img_path: /assets/img/
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

## A.4 CloudFront - SSL & SNI
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

| **Note (Important)**   |
|:--------------- |
| For using `HTTPS`:, |
|1. **You need to have the appropiate certificate public trusted certificate**. <br>|
|2. The certificate need to **match the CloudFront distribution name<br> either in viewer or origin protocol**|
|3. **You can not use self-signed certificate**.|

On the other side of **edge location** there is **origin**.
1. **Origin** is where `CloudFront` goes to get contains(S3, ELB, ALB, etc, ...). If the **origin** is S3, **then you don't need to worry about anything because S3 handle this natively**. **Origin are selected from behaviour**.

2. **Origin group** allows you to add **resiliency across those origins**. If you have one or more origin create xithin a distribution, **then you can create an origin group, group those origin together and hab=ve an origin group used by a behavior.

> For the viewer side, the certificate applied to `CloudFront` needs to match the `DNS name` of whatever your customers are using to acces `CloudFront` and then at the origin side, the certificate installed on any of your origins needs to match the `DNS Name` that ``CloudFront` is using to contact the origin.
>
There are actually a few categories of origins:
* `AMazon S3 buckets`(The viewer protocol policy match the origine protocol policy whether you're using HTTP or HTTP's, you also have the ability to pass custom header)
* `AWS Media Package channel Endpoints`
* `AWS Media Store Container Endpoints`
* `Everything else` (web servers also know as custom origins including S3 buckets configurins as a staic website). here you specify:
    * A **minimum origin SSL Protocol**: This configures the minimum protocol level that `CloudFront` would use when it establish a connection with your origon.
    * The **origin protocol policy** ( HTTP only, HTTPS only or to match viewer protocal policy)
    * Pass **custom header**

## A.5 CloudFront - Security - OAI & Custom Origins 

A.5.1 Security of the S3 origin

S3 can be use in two ways for origin in `CloudFront`:
* **S3 origin**
* Non **S3 origin** when you use S3 for hosting static website for example. It's use as **customs origin**

> **OAI (Origin Access Identity)** is only applicable to S3 origin.

* **OAI** is a type of identity
* * Explicitely **allow** or **deny** access to bucket and everything else is **implicitely deny**
*  To allow access via `CloudFront` to an S3 origin and **deny** direct access, **you need to create an origin access identity and associate that OAI with the `CloudFront` distribution**

The effect of doing that means that the edge locations gain this identity, **the origin access identity**, then we can create or adjust the bucket policy on the S3 bucket, *we add an explicit allow for the **origin access identity**. And in then in it's most secure form, we remove all others access, **living the implicit deny**.
At this point, any accesses from the edge loocations **are actually from the origin access identity** i.e the virtual identity that we've created and associated with the `CloudFront` distribution.

==> **Connection from the edge location is **allow** (becaus the OAI is explicitely allowed via the bucket policy)**

==> **Direct connection(from most users) would not have** the **OAI** associated with them, is **implicitely denied** from accessing the bucket.


### A.5.2 Security of the custom origin
For non S3 origin, we have two ways of implementing a more secire architecture:
* **First**: We can utilize **custom headers** where users use HTTPS to communicate with the edge location(we can configure the viewer protocol policy). In fact `HTTPS` is actually just `HTTP` **running inside a secure tunnel** by protecting the contents of that tunnel. We can also use the same protocol between the edge location and the origin, and this know as the **origin protocol policy**
* **Second**: AWs actually publicize the IP addresses of all of their services, so we can easily determine the IP ranges of the `CloudFront` edge locations. If we have the IP ranges that are used by `CloudFront` then **we can use traditional firewall around the custom origin and it's configure to allow xonnection in from the edge locations and deny anything else.**

## A.6 CloudFront Private Behaviors - Signed URLs & Cookies
`CloudFront` can run in two modes when it comes to content:
* The **first and the default** is public: In this mode, any content which is distributed via `Cloudfront` is public and can be accessed by any viwer.
* The **second is private**: In this mode we can either use **signed URL** or **signed Cookies**

`CloudFront` distribution are created with a single behavior.

| **Note (Important)** | |
|:--------------- |:--------------- |
| **Trusted signer**: Private distribution using `CloudFront`|

 **Signed URLs**: 
 * Access to one object
 * Legacy RTMP
 * If client doesn't supportcookies

 **Signed Cookies**:
* Access to group of object
* Maintenaing application URL
* Use for group of files/all files of a type

## A.6 CloudFront Geo

`CloudFront` is a global content delivery network and that content is distributed freely via all of the `CloudFront Edge locations`. If you enable **Geo restriction**, that change.

They are two main components that you need to understand:

| **Note (Important)** | |
|:--------------- |:--------------- |
| **CloudFront Geo-restriction** : Built-In **whitelist** or **blacklist** architecture **only works with countries**|
| **3 Partiy Geolocation** : It completely customizable. Use it to access third-party Geolocation services, or to restrict based on users or browsers or even the login state of an application|

**What makes third-party Geolocation different from `CloudFront` Geo-restriction?**

`Third party Geolocation`, or architecture that uses `third-party Geolocation` 
1. **Can be used for things beyond more accurate country checks**. 
2. It uses a **compute instance**, and it **works using signed URLs or cookies**. 
3. And so **it can be used to allow or deny access based on anything that the application has exposure to**.

So:

1. Restrictions based on **licences, user-data fields, headers or any other data except country** =====>  **Third-party Geolocation** architecture

2. Location restrictions **which go beyond just the country i.e states or latitude and longitude components** =====> **Third-party Geolocation** architecture

3. If it's **just country or country code** =====> **Built-in `Cloudfront Geo restriction feature set`**


If you like this content and would like to see more, please consider buying me a coffee! <a href="https://www.buymeacoffee.com/thierrykama" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" style="height: 60px !important;width: 217px !important;" ></a>
