---
author: quyennt
comments: true
date: 2014-03-05 11:12:46+00:00
excerpt: 'In part 1, I introduced Vagrant&Ansible and their power in automating development
  environment - the What & the Why. So in this part, I will show you the How: how
  to quickly setup you local environment using these tools.'
layout: post
slug: using-vagrant-ansible-to-automate-development-environment-part-2
title: Using Vagrant & Ansible to automate development environment - Part 2
wordpress_id: 306
categories:
- System
tags:
- ansible
- automation
- deployment
- vagrant
- virtualbox
---

# The How


In [part 1](http://quyennt.com/web-development/using-vagrant-ansible-automate-development-environment/), I introduced Vagrant&Ansible and their power in automating development environment - the What & the Why. So in this part, I will show you the How: how to quickly setup you local environment using these tools. There are two main parts:

<!-- more -->

**1. Setting up a new machine using Vagrant.**

**2. Setting up the latest nginx, php-fpm and mysql server on that machine, using Ansible**

Normally, It's sysadmin's job to helps new developers to get involved in the project as quickly as possible, without having to install all necessary tools to deploy the source code at local. However, if you want good food as your choice, you have to cook it yourself :-). So I would recommend developers, especially freelancers, to know these automating stuffs. It will make your life much easier.

Okay, let's the fun part comes in :-)


## Setting up new machine using Vagrant


[![vagrant](http://quyennt.com/wp-content/uploads/2014/03/vagrant.jpg)](http://quyennt.com/wp-content/uploads/2014/03/vagrant.jpg)

**1.** As explained in part 1, you should never set up your environment on your real machine, use virtual machine instead. You will need [VirtualBox](https://www.virtualbox.org/wiki/Downloads), install it.

**2.** You will need a little NAT network configuration for VirtualBox, follow this [link](http://emmanuelbernard.com/blog/2012/02/28/configuring-virtualbox-guests-to-access-the-internet-and-be-accessible-from-the-host/) or do as quoted below:



	
  * On VirtualBox, open up (general) preferences -> network

	
  * Add a host-only network (if none exist)

	
  * Configure the host-only network with an IP address (eg `192.168.56.1`)

	
  * Optionally enable the DHCP server and make sure to leave room for static addresses (eg by setting lower address bound to `192.168.56.101`


**3.** Install [Vagrant](http://www.vagrantup.com/downloads.html) & check if Vagrant works:

    
    $ vagrant -v
    Vagrant version 1.3.5


**4.** Normally, to create a new virtual machine of whatever OS you will need an OS disk image (.iso file) to install from. But with Vagrant, you will need a vagrant box instead, check this link to download your appropriated OS: http://www.vagrantbox.es/. For me, I will choose two boxes of different OS as below:

- https://github.com/2creatives/vagrant-centos/releases/download/v0.1.0/centos64-x86_64-20131030.box - CentOS 6.4 x86_64

- http://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-i386-vagrant-disk1.box - Official Ubuntu 12.04

**5.** Prepare you machine directory as mine below: (the structure is up to you to decide, I show mine just for easy explanation)

    
    $ tree VM
    VM
    ├── centos64_56.101    ## dir contains config for centos 
    ├── centos64-x86_64-20131030.box
    ├── precise-server-cloudimg-i386-vagrant-disk1.box
    └── ubuntu12_56.102    ## dir contains config for ubuntu


**6.** Execute these commands below to create the Vagrantfile, which is the configuration file:

    
    $ cd centos64_56.101
    
    ## prepare environment: vagrant box add [box-name] [path/to/box/file], for example: 
    $ vagrant box add centos64_56.101 ../centos64-x86_64-20131030.box
    
    ## Create Vagrantfile file that contain every needed configuration
    $ vagrant init


**7.** There are a lot of config in the Vagrantfile, but for basic implementation, you just need to un-comment and edit the following:

    
    # config.vm.box = “[box-name]”
    config.vm.box = “centos64_56.101”
    
    # config.vm.hostname = "[hostname]" => VM's hostname, will appear as vagrant@[hostname]
    config.vm.hostname = "test"
    
    # config.vm.box_url = "[path/to/box/file]",
    config.vm.box_url = "/home/quyennt/Documents/VM/centos64-x86_64-20131030.box"
    
    # set IP of the VM
    config.vm.network :private_network, "192.168.56.101"
    
    # Share folder between the host and the VM
    # config.vm.synced_folder, "[path/to/host/folder]", "[path/to/vm/folder]"
    config.vm.synced_folder, "/home/quyennt/Sites", "/var/www/html"


**8.** Final step:

    
    ## This command must be executed inside the folder containing Vagrantfile
    $ vagrant up
    [default] VM already created. Booting if it's not already running...
    [default] Clearing any previously set forwarded ports...
    [default] Forwarding ports...
    [default] -- 22 => 2222 (adapter 1)
    [default] Creating shared folders metadata...
    [default] Clearing any previously set network interfaces...
    [default] Preparing network interfaces based on configuration...
    [default] Booting VM...
    [default] Waiting for VM to boot. This can take a few minutes.
    [default] VM booted and ready for use!
    [default] Configuring and enabling network interfaces...
    [default] Mounting shared folders...
    [default] -- v-root: /vagrant
    [default] -- v-data: /var/www/html


That's it! Your VM is up and running! You should see the VM is running in the VirtualBox Manager. You can ssh to this VM using the command below:

    
    $ vagrant ssh




## Appendix: Useful commands & configurations




### Commands

Shutdown VM

    $ vagrant halt
    
    ## Use this after whenever you modify Vagrantfile
    $ vagrant reload

In case you want to recreate the box, shutdown and delete the existing box as below, then remove the VM from the VirtualBox VM folder

    $ vagrant box remove [box-name] [provider]
    $ vagrant halt
    $ vagrant box remove centos64_56.101 virtualbox
    
Or you can simply destroy it, this way the VM will be deleted from from the VirtualBox VM folder

    $ vagrant destroy
    $ vagrant box remove centos64_56.101 virtualbox


### Configurations


By default, the owner of the shared folder in the VM is vagrant, if you want to change it to, ex: `www-data`, edit the Vagrantfile to change the `config.vm.synced_folder` as below:
    
    config.vm.synced_folder "/home/quyennt/Sites", "/var/www/html", :owner => "www-data", :group => "www-data"


What if you want to change the default allocated memory/CPU cores? Add to the Vagrantfile the following lines:

    
    # Add 4Gb RAM
    config.vm.customize ["modifyvm", :id, "--memory", "4096"]
    # Using 2 cores cpu
    config.vm.customize ["modifyvm", :id, "--cpus", "2"]



## Troubleshooting


When you remove a box, remember to remove accordingly its IP from your `~/.ssh/known_hosts`. Otherwise you will get error when recreating it with the same IP.

    
    # ssh-keygen -f "/path/to/your/known_hosts" -R [IP-to-remove]
    ssh-keygen -f "/home/quyennt/.ssh/known_hosts" -R 192.168.56.101


But remember to change the box name and IP when you're creating another VM.

Always execute vagrant commands inside the folder containing Vagrantfile so that it understand what VM you are working on.

when you do `vagrant ssh`, it will use the **default ssh user: vagrant**. So you can always do normal ssh as below:

    
    ## user:vagrant; pass: vagrant
    $ ssh vagrant@192.168.56.101
    
    ## user:root, pass: vagrant
    $ ssh root@192.168.56.101




## Conclusion


To this point, the CentOS machine is up and running! You can go to the ubuntu folder and repeat the same steps and you will get another VM. But remember to change the box name and IP!

In next part, I will show you how to use Ansible to install all the needed tools for the VM, and how to use Ansible as a provisioning of Vagrant, so that the tools could be installed the moment the vagrant box is being constructed.

Part 1: [http://quyennt.com/system-admin/using-vagrant-ansible-automate-development-environment/](http://quyennt.com/system-admin/using-vagrant-ansible-automate-development-environment/)[
](http://quyennt.com/web-development/using-vagrant-ansible-automate-development-environment/)Part 3:[ http://quyennt.com/system-admin/using-vagrant-ansible-to-automate-development-environment-part-3/](http://quyennt.com/system-admin/using-vagrant-ansible-to-automate-development-environment-part-3/)
