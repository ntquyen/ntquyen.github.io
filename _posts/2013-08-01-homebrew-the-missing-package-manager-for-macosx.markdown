---
author: quyennt
comments: true
date: 2013-08-01 11:15:09+00:00
excerpt: If you are regularly working with linux, unix system, your would be familiar
  with some command-line package managers such as yum for Fedora/Centos, or apt-get
  for Ubuntu... that help you, by just one simple command line, to install almost
  every open-source tools you need for your development. But what if you are using
  a Mac OS X and also need to install similar tools to test locally? Yes, Homebrew
  will help you.
layout: post
slug: homebrew-the-missing-package-manager-for-macosx
title: 'Homebrew: The Missing Package Manager for Mac OS X'
wordpress_id: 126
categories:
- System
tags:
- brew
- homebrew
- Mac OS X
- open source
- package manager
---

If you are regularly working with linux, unix system, your would be familiar with some command-line package managers such as `yum` for Fedora/Centos, or `apt-get` for Ubuntu... that help you, by just one simple command line, to install almost every open-source tools you need for your development. But what if you are using a Mac OS X and also need to install similar tools to test locally? Yes, **Homebrew** will help you. Unfortunately, brew isn't installed by default, you have to install it yourself.

<!-- more -->


<blockquote>**Homebrew is the open-source command-line package manager for Mac OS X**. It has thousand of packages (also known as formulas) contributed by users. It will help you to install almost every tools you need, with all their dependencies, by a single command line. Find out more via its [official site](http://brew.sh/) and [GitHub](https://github.com/mxcl/homebrew/wiki).</blockquote>


Table of Contents
I. Requirement
II. Installation
III. Troubleshooting
IV. Best Practices


## I. Requirements


To install Homebrew, it is recommended that your OS version is 10.6 or higher. And [Xcode](http://itunes.apple.com/us/app/xcode/id497799835) or at least [Command line tools for Xcode](https://developer.apple.com/downloads) must be installed before.


## II. Installation & Basic usages


The good news is, the installation is very easy. All you need to do is entering the command below in the terminal:

    
    $ ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"


That's it! Your system should be ready to brew now! To install a package, just run with the command below

    
    $ brew install package_name


Remember to replace "package_name" with the package you want to install and and make sure that it exists in the supported [formulas list](https://github.com/mxcl/homebrew/tree/master/Library/Formula/).


## III. Troubleshooting




### 3.1 Update download source link


Sometime the tool installed with brew is not the latest stable version. You may want to modify the download link by running this command:

    
    $ brew edit package_name


Replace "package_name" with your package. It will open an editor like below (in this example, I tried to modify innotop fomula) to let you modify the link (and the corresponding sha1 if neccessary):


[![$ brew edit innotop](http://quyennt.com/wp-content/uploads/2013/08/Screen-Shot-2013-08-02-at-12.56.17-AM.png)](http://quyennt.com/wp-content/uploads/2013/08/Screen-Shot-2013-08-02-at-12.56.17-AM.png)





### 




### 3.2. Verify with brew doctor


Normally, you should be able to use brew right after the installation. However, you may try the following command to verify your installed packages with homebrew:

    
    $ brew doctor


If you encounter some warnings like below

    
    Warning: /usr/bin occurs before /usr/local/bin


just open the `/etc/paths`  by `vim` command and put the `/usr/local/bin` above the `/usr/bin`

You may also get some other warnings such as:

    
    <span style="text-decoration: underline;" data-mce-style="text-decoration: underline;">Warning</span>: Unbrewed dylibs were found in /usr/local/lib.
    If you didn't put them there on purpose they could cause problems when
    building Homebrew formulae, and may need to be deleted.
    
    Unexpected dylibs:
    /usr/local/lib/libhashkit.2.dylib
    /usr/local/lib/libmemcached.11.dylib
    /usr/local/lib/libmemcachedutil.2.dylib
    /usr/local/lib/libnetsnmp.30.dylib
    /usr/local/lib/libnetsnmpagent.30.dylib
    ...


If you're not sure about those libraries, I recommended you to ignore the warning because Homebrew will still work fine. But if you still want to keep your system clean, you may want to do a back-up before removing them.


## 




## IV. Best practices


**1.** Keep in mind that you already have brew now,  **try brew your package first, before downloading the source and install it manually**. That will help you save a lot of work!

**2.** After each installation of a package, you should run `brew doctor` to verify.
**3.** From time to time, you should also run the command below:

    
    $ brew test-bot


This command will first update your brew and a dozen of brew commands, including `brew doctor`, to make sure your brew works well. Some output of the command:

    
    $ brew test-bot
    ==> brew update								 PASSED
    ==> brew doctor								 FAILED
    Warning: "config" scripts exist outside your system or Homebrew directories.
    `./configure` scripts often look for *-config scripts to determine if
    software packages are installed, and what additional flags to use when
    compiling and linking.
    
    Having additional scripts in your path can confuse software installed via
    Homebrew if the config script overrides a system or Homebrew provided
    script of the same name. We found the following "config" scripts:
    
        /Library/Frameworks/Python.framework/Versions/2.7/bin/python-config
        /Library/Frameworks/Python.framework/Versions/2.7/bin/python2-config
        /Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7-config
    
    ==> brew --env								 PASSED
    ==> brew --config							 PASSED
    ==> brew tests								 PASSED
    ==> brew readall							 PASSED
    ==> brew fetch --build-bottle pypy					 PASSED
    ==> brew install --verbose --build-bottle pypy				 PASSED
    ==> brew audit pypy							 PASSED
    ==> brew bottle pypy							 PASSED
    ==> Bottling pypy 2.1.0...
    ./pypy-2.1.0.lion.bottle.tar.gz
    bottle do
      sha1 '73d2e1457d115db75dd91e4ce8a8da17a9822101' => :lion
    end
    ==> brew uninstall --force pypy						 PASSED
    ==> brew install pypy-2.1.0.lion.bottle.tar.gz				 PASSED
    ==> brew uninstall --force pypy						 PASSED
    ==> git checkout  master						 PASSED


That's it. You will find that using Homebrew is very easy, just like yum or apt-get. It saved me a lot of time to install many tools such as innotop, memcached, redis, sphinx... for my PHP projects. And I'm sure it will help you a lot, too!

Table of Contents
I. Requirement
II. Installation
III. Troubleshooting
IV. Best Practices
