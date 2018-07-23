---
layout: post
title: Moving a Google site to S3
image: "assets/images/php-html-code.jpg"
---
> _Question: I bought my own domain name through Google Domains. I don't like the
> limitations of Google Sites for hosting a simple static Web site, and I would
> like to use Amazon Web Service's capabilities for hosting a static Web site on
> S3, but I still want to keep my GMail.  How can I point my domain at my S3 site,
> but keep Google as my e-mail provider?_

## Process Overview
1. Set up S3 Bucket for Static Web Hosting
2. Create Route 53 A Record to Point Your Domain to S3 Bucket
3. Change Google Domain Hosting Config to Use AWS's Name Servers
4. Configure Route 53 to point MX records to Google's Mail Servers
5. Set Up DNS Anti-Spoofing

https://toolbox.googleapps.com/apps/checkmx/
https://blog.andrewray.me/setting-up-gsuite-gmail-custom-domains-with-aws-route53/
https://medium.com/@limichelle21/connecting-google-domains-to-amazon-s3-d0d9da467650
