---
author: quyennt
comments: true
date: 2014-02-25 16:26:27+00:00
excerpt: You remember once upon the time when you had to setup LAMP stack and every
  other tool manually? Even more painful, for each machine (if you have more than
  one), you have to do the same thing, yes, manually? But not anymore, Vagrant and
  its folks Puppet, Chef or Ansible will automate everything for you.
layout: post
slug: using-vagrant-ansible-automate-development-environment
title: Using Vagrant & Ansible to automate development environment - Part 1
wordpress_id: 254
categories:
- System
tags:
- ansible
- automation
- deployment
- vagrant
---

## The What & the Why


Let me tell you a story about how a beginner in PHP became tougher in the field.


<blockquote>**It is not only about everyday coding, but trying new things and staying on top of changes.**</blockquote>


This story took me back to 1.5 year ago. Using a Mac OS X, I installed MAMP app and launched my first "hello world" web application :-). Then I quickly realized that I need to install myself each module of the stack: Apache, PHP and MySQL to see more clearly how they work together. So, I said goodbye to fancy interfaces and start my first command lines in terminal. I even got rid of PHPMyAdmin and use terminal to work with databases.

<!-- more -->

But there was not enough. Thanks to my college whom I consider as my mentor in system administration, I finally got though a problem that was always there, but I never tried to solve or even noticed it.


<blockquote>**It is** **the difference between web development environments.**</blockquote>


I use Mac OS X to code, but the production servers are all using CentOS. That drove me to many frustrated situations! Here I note two of them:



	
  * Every time I need to use a tool, I have to tried it first in my local machine, make sure things work out before install it in production. So I have to figure out how to install it both OS X and CentOS. Yes, it's not a problem if the tool can be installed via Homebrew (for OS X) and yum (for CentOS); but imagine if you must install it from source and deal with all dependencies!

	
  * The "it works on my machine" issue happened many times.


So, I started using VirtualBox and setup my local dev server in the same CentOS version as those in production. This virtual  machine (VM) share my source code directory with my host, so that I can code normally like before. The only difference is that the whole server (my localhost) with every other stuff are now moved to that virtual machine. This change offered not only me, but my whole dev team, surprising benefits!

	
  * First, moving your localhost into virtual machine can save you a lot of resources like CPU or RAM. 1GB RAM assigned for each VM is more than enough and it will run everything for you.

	
  * You setup your own server and copy it to other team members, and all of you will have the same working environment. Totally solve the "it works on my machine" issue.


Well, things are getting really better now. Then I read [PHP The Right Way](http://www.phptherightway.com/) blog and notice a tool even more powerful to help you automate your local deployment: [Vagrant](http://vagrantup.com/).

You remember once upon the time when you had to setup LAMP stack and every other tool manually? Even more painful, for each machine (if you have more than one), you have to do the same thing, yes, manually? But not anymore, Vagrant and its folks Puppet, Chef or Ansible will automate everything for you.


<blockquote>And what I mean by "automate"? Well, I mean if you want to install the vim application on 100 servers,  you don't need to ssh to every server and do one same thing over and over like crazy. Instead, you sit at a machine, configure a few things, run a command, and all 100 servers have vim ready!</blockquote>


[caption id="attachment_265" align="aligncenter" width="300"][![abc](http://quyennt.com/wp-content/uploads/2014/02/automation-figured-300x224.jpg)](http://quyennt.com/wp-content/uploads/2014/02/automation-figured.jpg) Automation figured. Source: http://www.ircc.iitb.ac.in/[/caption]

Concretely, Vagrant will help you get a newly VM with every basic thing perfectly configured (network, IP, share folders, etc.) in less than 3 minutes. and Puppet, Chef or Ansible will help you working with multiples servers without ssh to each one.

And like you, I am just a beginner walking the first steps in the automation world, I will share as I experiment these awesome tools. Please stay tuned!

Part 2: [http://quyennt.com/system-admin/using-vagrant-ansible-to-automate-development-environment-part-2/](http://quyennt.com/system-admin/using-vagrant-ansible-to-automate-development-environment-part-2/)Part 3:[ http://quyennt.com/system-admin/using-vagrant-ansible-to-automate-development-environment-part-3/](http://quyennt.com/system-admin/using-vagrant-ansible-to-automate-development-environment-part-3/)
