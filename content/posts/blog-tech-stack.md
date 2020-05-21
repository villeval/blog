---
title: "Building a blog with Hugo and AWS"
date: 2020-05-21T00:00:00+02:00
description: "A short tour into the building blocks of my blog."
author: "Ville Valtonen"
draft: true
---

### Background

When I decided to create a personal website, I initially built it with React. Partially because I wanted more experience about the technology but also partly because I thought single-page applications are the way to go in the modern age of the internet. In the beginning the website was fairly simple with couple of pages, but at some point I wanted to start writing a blog, so naturally the site started to grow in terms of codebase. The logic was still fairly simple: a list to view all the posts in a chronological order and individual pages for the posts itself. Although it wasn't super complex, the problem was that the content was tied into the logic of the page and I was "vendor-locked" into my own code. The page was also rendered at client-side so it was not SEO-friendly either. 

One day I came across with the source code of a blog I've been reading for some time and saw that it was built with this static-site generator called Hugo. After a brief investigation of the technology, I decided it was time to migrate my site from React to Hugo. The primary reasons were the simplicity of the content producing, static site as an outcome and the fact it was lightning fast as it's based on Go.

### The site and content producing

Like previously mentioned, the site is built with [Hugo](https://gohugo.io), which is a static site generator. With Hugo I can use markdown to write the actual posts and publish new posts with ease. With Hugo you can build your site based on small components called "partials". For example my navigation bar, footer etc. are partials, which are added on the pages automatically as they're added to the template of the single posts, which Hugo uses to generate the content pages based on my markdown-files. If I need changes on those partials, I have to update them only in one place. My site has a custom styling, but there are also lots of ready-to-use themes available if you don't want to spend time on CSS.

### Hosting

Initially I picked up AWS because I use it at my work on daily basis and I'm proficient with it. The domain was purchased via AWS as well, so setting up the hosting and updating the content is pretty straight forward. The content is built on my machine and I sync it directly to the S3-bucket I'm using for hosting the files. Although there isn't any traffic other than transfering the public assets of my website to client, in general it's a must to use SSL-certificates to provide HTTPS-connections to avoid any warnings about not secure website from browsers. Since S3 doesn't not support SSL-certificates itself, I requested a SSL-certificate for my custom domain from AWS and set up a CloudFront distribution with that certificate for secure connections. CloudFront is an AWS-hosted CDN and in my case it caches the content of my S3 on multiple locations to provide quicker access to from different sides of the world. The routing for the domain is handled with an alias record to my CloudFront distribution within Route53.

The cost of hosting my site like this is pretty negligible and would remain so although the amount of traffic would increase a lot. The total cost is accumulated by the cost of the domain, one hosted zone in Route53 and the amount of transfered data from the AWS resources i.e. S3 and CloudFront. Currently the total cost of hosting is around 1-1.5 euros per month.

\
Thanks for reading and see you next time!

PS. If you're interested in, the source code of my website is available in [Github](https://github.com/villeval/blog).