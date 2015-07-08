---
author: quyennt
comments: true
date: 2014-03-20 04:16:51+00:00
excerpt: 'When you''re using Vagrant on top of VirtualBox to setup your development
  environment, sharing folder between host & virtual machine using vm.synced_folder.
  You may not be able to modify your  javascript files (which is located in shared
  folder). Because every modification will cause the error SyntaxError: Unexpected
  token ILLEGAL in browser dev console.'
layout: post
slug: chrome-js-syntaxerror-unexpected-token-illegal-caused-by-vagrant-synced-folder
title: 'Chrome JS SyntaxError: Unexpected token ILLEGAL caused by Vagrant synced folder'
wordpress_id: 357
categories:
- Web Development
tags:
- javascript
- sendfile
- shared folder
- troubleshooting
- vagrant
---

This bug took me half a day to figure out.


## How to reproduce


When you're using Vagrant on top of VirtualBox to setup your development environment, sharing folder between host & virtual machine using `vm.synced_folder`. You may not be able to modify your  javascript files (which is located in shared folder). Because every modification will cause the error `SyntaxError: Unexpected token ILLEGAL` in browser dev console.

This was NOT file encoding problem, because even you use vim to modify, the error's still there.

<!-- more -->


## Solution


This was raised as a [VirtualBox bug](https://github.com/mitchellh/vagrant/issues/351#issuecomment-1339640) related to `sendfile` which can result in corrupted or non-updating files.

So the simplest solution is to deactivate `sendfile` in any web servers you may be running.

**In nginx:**

    
    sendfile off;


** In apache:**

    
    EnableSendfile Off


There's another solution is to use [NFS](http://en.wikipedia.org/wiki/Network_File_System_%28protocol%29) other than the default shared folder implementation of VirtualBox. But this is more complicated, so I haven't tried. Please let me know in comment below if you tried this solution!

**Reference:**

[http://stackoverflow.com/questions/9479117/vagrant-virtualbox-apache2-strange-cache-behaviour](http://stackoverflow.com/questions/9479117/vagrant-virtualbox-apache2-strange-cache-behaviour)

[http://docs.vagrantup.com/v2/synced-folders/virtualbox.html](http://docs.vagrantup.com/v2/synced-folders/virtualbox.html)
