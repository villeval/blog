---
title: "Building a website with Hugo and AWS"
date: 2020-05-21T00:00:00+02:00
description: "A short tour into the building blocks of my website."
author: "Ville Valtonen"
draft: false
---

### Why I chose Hugo?

When I decided to create a personal website, I initially built it with React. Partially because I wanted more experience about the technology but also partly because I thought single-page applications are the way to go in the modern age of the internet. The logic and code for the website was still fairly simple, but the problem was that the content was tied into the codebase of the page and I was "vendor-locked" into my own code. The page was also rendered at client-side so it was not SEO-friendly either.

One day I came across with the source code of a blog I've been reading for some time ([jessfraz.com](https://jessfraz.com)) and saw that it was built with this static-site generator called Hugo. After a brief investigation of the technology, I decided it was time to migrate my site from React to Hugo. The primary reasons were the simplicity of the content producing and static site generator as well as the super low build time. I'm also a huge fan of Go.

### Building the site with Hugo

Like previously mentioned, this site is built with [Hugo](https://gohugo.io). With Hugo I can use markdown to write posts and publish them with ease. With Hugo you can build your site based on small component-like things called templates and partials, which make your site pretty modular. Although my site has a custom styling, there are also lots of ready-to-use themes available if you don't want to spend time on CSS and start writing right away.

### Hosting with AWS

I'm hosting my website with AWS, since it was familiar for me from my work and the previous incarnation of my website was also hosted in AWS. The components I'm using are Route53 for routing, S3 for storing the (static) files and CloudFront as a CDN to cache the files on multiple locations and also provide HTTPS-connections with SSL-certificate (S3 doesn't support SSL-certificates separately). Whenever I create a new post, I just build the site and sync it to the S3 bucket.

The cost of hosting my site like this is pretty negligible and would remain so although the amount of traffic would increase a lot. The total cost is accumulated by the cost of the domain, one hosted zone in Route53 and the amount of transfered data from the AWS resources i.e. S3 and CloudFront. Currently the total cost of hosting is around 0.5-1.5 euros per month plus the annual fee for the domain.

\
Thanks for reading and see you next time!

PS. If you're interested in, the source code of my website is available in [Github](https://github.com/villevaltonen/blog).
