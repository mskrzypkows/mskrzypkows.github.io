---
bg: "ricardo-gomez-angel-tunnel.jpg"
layout: post
title:  "Remote work using SSH"
crawlertitle: "Remote work using SSH tunnel"
summary: "How to connect to work's computer using SSH reverse tunnel and easily synchronize data"
date:   2018-02-05
categories: posts
tags: ['tech']
author: mskr
---

**Under construction**

Remote work sometimes is not possible without access to yours work's PC, due to some reasons e.g.:
- Special hardware which you don't have in your home, e.g. fast GPU card
- Access some company's data which can be done only from work's PC
- Synchronization of data, sometimes it's easier to have all data on single PC than copy it every time you want to work remotely

Let's digg into details, what we will need?

1. Linux shell acount.
2. Magic ssh commads.
3. Synchronization script.

## Linux shell account

Work's PC needs to connect to some remote server and estabilish constant ssh connection. Then as we connect from home, we'll also connect to the remote server and use reverse ssh tunnel to access work's PC. This can be done even if we have a NAT in work because all commands are sent to work's PC only as a respond to its messages to the remote server.

One of the simplest way to create shell account on the remote server is to sing up to Amazon AWS, and use 12 months AWS Free Tier.
After cration of AWS account we chose free t2.micro EC2 instance. It's important to chose proper location of the instance (the closest to your work and home, e.g. Frankfurt for Europeans). Then we chose operating system, I recomend Ubuntu Server 16.04 LTS.

## Magic ssh commads


From home's PC you run:

{% highlight bash %}
ssh -p 2210 -i ~/privateKey.pem worksPCLogin@*-*-*-*-*.eu-central-1.compute.amazonaws.com
{% endhighlight %}




*Thanks Jakub and Rafał who shared their knowledged used in this post.*

*Side Photo by Ricardo Gomez Angel on Unsplash*
