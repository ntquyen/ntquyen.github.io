---
author: quyennt
comments: true
date: 2013-08-26 04:10:05+00:00
excerpt: This is my first blog about monitoring topic. In this blog, I wanna show
  you the most basic built-in command-line tools that I usually use to get to know
  about linux system information, such as OS distribution, CPU, memory and disk space.
layout: post
slug: basic-linux-monitoring-tools
title: Basic linux monitoring tools
wordpress_id: 194
categories:
- System
tags:
- linux
- monitoring
- session
- tool
---

This is my first blog about monitoring topic. In this blog, I wanna show you the most basic built-in command-line tools that I usually use to get to know about linux system information, such as OS distribution, CPU, memory and disk space.

<!-- more -->


## **1. OS distribution name and version::**


The OS distribution is stored in /etc/*-release file (/etc/lsb-release on Ubuntu, /etc/RedHat-release on CentOS)

    
    $ cat /etc/*-release


Sample output on Ubuntu:

    
    DISTRIB_ID=Ubuntu
    DISTRIB_RELEASE=10.04
    DISTRIB_CODENAME=lucid
    DISTRIB_DESCRIPTION="Ubuntu 10.04.4 LTS"


or on CentOS:

    
    CentOS release 6.4 (Final)
    LSB_VERSION=base-4.0-amd64:base-4.0-noarch:core-4.0-amd64:core-4.0-noarch:graphics-4.0-amd64:graphics-4.0-noarch:printing-4.0-amd64:printing-4.0-noarch




## 2. Check whether it is 32-bit or 64-bit



    
    $ getconf LONG_BIT
    64




## 3. Kernel & gcc version



    
    $ cat /proc/version
    Linux version 3.9.3-x86-linode52 (maker@build) (gcc version 4.4.5 (Debian 4.4.5-8) ) #1 SMP Mon May 20 09:32:28 EDT 2013




## 4. Then, you may wanna check the CPU information:



    
    $ lscpu
    Architecture:          i686
    CPU(s):                8
    Thread(s) per core:    8
    Core(s) per socket:    1
    CPU socket(s):         1
    Vendor ID:             GenuineIntel
    CPU family:            6
    Model:                 45
    Stepping:              7
    CPU MHz:               2600.090
    L1d cache:             32K
    L1i cache:             32K
    L2 cache:              256K
    L3 cache:              20480K


or you can use the command below to get more detail about cpu:

    
    $ cat /proc/cpuinfo




## 5. And the memory:



    
    $ free -mt
                 total       used       free     shared    buffers     cached
    Mem:          5850       4923        926          0        179       2991
    -/+ buffers/cache:       1753       4097
    Swap:         5983        129       5854
    Total:       11834       5053       6781


where,


_ -m_




print the memory sizes in megabyte (-k for kilobyte, -g for gigabyte)




_-t_




display the total memory size


or you can try the command below to get more detail about memory:

    
    $ cat /proc/meminfo




##  6. Check **disk space with all mounted partitions**



    
    $ df -Th
    Filesystem    Type    Size  Used Avail Use% Mounted on
    /dev/mapper/vg_localhost-lv_root
                  ext4     49G   22G   24G  48% /
    tmpfs        tmpfs    2.9G     0  2.9G   0% /dev/shm
    /dev/sda1     ext4    485M   63M  397M  14% /boot
    /dev/mapper/vg_localhost-lv_home
                  ext4    4.7G  208M  4.3G   5% /home
    192.168.166.12:/audio
                   nfs    1.2T  956G  2.7G  99% /audio
    192.168.166.13:/video
                   nfs    1.2T  1.1T  8.5G 100% /video


where,


-T




print the file system type (the Type column)




-h, --human-readable




print sizes in human readable format (e.g., 1K 234M 2G)


Use the command below if you just want to get the size of a directory

    
    $du -sh /path/to/folder


where,


_-s, --summarize_




print to total size of the folder. If you don't use this param, it will print all the files in that folder




_-h, --human-readable_




print the size in Kilobytes, Megabytes or Gigabyte





## 7. You may wanna know about other users's current activities on the system:



    
    $ w
    09:18:13 up 30 days, 14:43,  1 user,  load average: 0.95, 1.02, 1.19
    USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
    quyennt  pts/0    11.11.11.7       08:14    0.00s  0.14s  0.03s w




## Summary


Voilà, the commands above are very basic tools. You may know all about them, but you may not know how it could save you a lot of effort solving the problem :-).

Such circumstance came to me once that took me a day to solve the problem. My website had been running very well, then one day it was down. After analyzing the code for a while, I figured out that because of the session, it ain't working at all but I still had no idea why. After researching and thanks for my friend's help, I discovered that due to the full disk space!! The session was stored as files and it could no longer generate. And that was the moment when I first realized the importance of monitoring :).

So, the lesson for me after that time is, if your website is suddenly down without reason, the cpu, ram and disk space are the first things to check before debugging your code.

There's still a lot more cool, advanced tools that could give you eyes on every corner of your system. And I'd like to save them for the next article. Hope this help for now
