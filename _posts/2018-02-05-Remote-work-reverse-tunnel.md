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

Let's dig into details, what we will need?

1. Linux shell account.
2. Magic ssh commands.
3. Synchronization script.

## Linux shell account

Work's PC needs to establish constant ssh connection to some remote server. Then as we connect from home, we'll also connect to the remote server and use reverse ssh tunnel to access work's PC. This can be done even if we have a NAT in work because all commands are sent to work's PC only as a respond to its messages to the remote server.

One of the simplest way to create shell account on the remote server is to sing up to Amazon AWS, and use 12 months AWS Free Tier.
After creation of AWS account we chose free t2.micro EC2 instance. It's important to chose proper location of the instance (the closest to your work and home, e.g. Frankfurt for Europeans). Then we chose operating system, I recommend Ubuntu Server 16.04 LTS.

## Magic ssh commands

On work's PC we connect to AWS Ubuntu machine using RSA private key generated for the account:

{% highlight bash %}
autossh -o "PubkeyAuthentication=yes" -o "PasswordAuthentication=no" -i /location/of/the/key/key.pem -R 0.0.0.0:2210:localhost:22 ubuntu@abc-00-00-00-00.eu-central-1.compute.amazonaws.com -o "ServerAliveInterval 20" -o "ServerAliveCountMax 3" -f -N
{% endhighlight %}

The best option is to add this command to the crone, so if work's PC will be restarted, it'll connect to AWS machine by itself. We run by normal user:
{% highlight bash %}
crontab -e
{% endhighlight %}
then add autossh command on reboot, at the end of file:
{% highlight bash %}
@reboot autossh -o "PubkeyAuthentication=yes" -o "PasswordAuthentication=no" -i /location/of/the/key/key.pem -R 0.0.0.0:2210:localhost:22 ubuntu@abc-00-00-00-00.eu-central-1.compute.amazonaws.com -o "ServerAliveInterval 20" -o "ServerAliveCountMax 3" -f -N
{% endhighlight %}


From home's PC you run below command. It's worth to notice that we use work's PC user name login with AWS domain:

{% highlight bash %}
ssh -p 2210 -i /location/of/the/key/key.pem worksPCLogin@abc-00-00-00-00.eu-central-1.compute.amazonaws.com
{% endhighlight %}

## Synchronization script

For easy editing files, better do it locally and copy for work's PC. You can do it automatically using my [script](https://github.com/mskrzypkows/Copy-changed-files) :-)
First mount work's PC home folder to some folder on home's PC, you will need `sshfs` installed and `Work` directory created.

{% highlight bash %}
sshfs -o IdentityFile=/location/of/the/key/key.pem -p 2210  worksPCLogin@abc-00-00-00-00.eu-central-1.compute.amazonaws.com:/home/username /home/username/Work
{% endhighlight %}

After adding execution rights:
{% highlight bash %}
chmod +x copy_changed_files.py
{% endhighlight %}

You can run my script sepcifying source and destination directory:
{% highlight bash %}
./copy_changed_files.py /home/usename/Projects/Project1 /home/username/Work/Projects/Project1
{% endhighlight %}

Script will scan source directory, remember all files modification time, if any file changes it will be copied to appropriate destination location.

Good luck and have a fun! :]



*Thanks Jakub and Rafał who shared their knowledge used in this post.*

*Side Photo by Ricardo Gomez Angel on Unsplash*
