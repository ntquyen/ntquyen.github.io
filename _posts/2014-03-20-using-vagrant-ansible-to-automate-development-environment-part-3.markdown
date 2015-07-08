---
author: quyennt
comments: true
date: 2014-03-20 11:22:46+00:00
layout: post
slug: using-vagrant-ansible-to-automate-development-environment-part-3
title: Using Vagrant & Ansible to automate development environment – Part 3
wordpress_id: 330
categories:
- System
tags:
- ansible
- automation
- deployment
- vagrant
---

In the [previous part]({% post_url 2014-03-05-using-vagrant-ansible-to-automate-development-environment-part-2 %}), I have showed you how to use Vagrant to quickly setup a new virtual machine (VM) with some basic configurations such as network config, shared_folder, etc.  At this moment, your VM should be ready to install the tools needed for your development. But it is not just that, with the help of one of its provisioners like Puppet, Chef or Ansible, you can prepare a complete development environment without further manual installations!


<blockquote>Puppet, Chef, and Ansible were all built with that very goal in mind: to make it much easier to configure and maintain dozens, hundreds, or even thousands of servers.</blockquote>


In this post, I will show how to use Ansible to work with remote machines. First, I will introduce the basic of Ansible, Ansible modules and playbooks. Then I will show how to use Vagrant with Ansible to deploy your local development environment.

<!-- more -->


## Basics of Ansible




### 1. Install Ansible.


Because Ansible is built in Python, so you will need Python 2.4 or later, which is installed in all UNIX machine. Follow this [link](http://docs.ansible.com/intro_installation.html#id11) and you will have it installed without trouble.


### 2. How Ansible communicates to remote machines


You need to know that Ansible communicates with remote hosts via `SSH`. So, there are two things you have to notice :

* The remote user that Ansible connect to
* The current user


If we don't specify anything about these users, Ansible will assume the current username as remote user. The best way to avoid all connection problem is to copy the public key to remote hosts and specify explicitly the remote user in Ansible commands.

{% highlight bash %}

## Copy public key to user vagrant on host 192.168.56.101
$ ssh-copy-id -i ~/.ssh/id_rsa.pub vagrant@192.168.56.101

{% endhighlight %}



### 3. Define the remote hosts for work

The remote hosts are simply defined as the syntax below:

{% highlight ini %}
## webservers is the group name of similar hosts, below it is a list the IPs/hostnames
[webservers]
192.168.56.101
{% endhighlight %}

You might wanna check out the default host file at `/etc/ansible/hosts` for more details.

### 4.  First commands
Now try our first command to know if Ansible work!

{% highlight bash %}
## ping all the hosts defined in default hosts file /etc/ansible/hosts
$ ansible all -m ping -u vagrant
192.168.56.101 | success >> {
    "changed": false,
    "ping": "pong"
}

## ping the specified group of hosts you defined
$ echo -e "[webservers]\n192.168.56.11" > hosts
$ ansible webservers -i ./hosts -m ping -u vagrant
192.168.56.101 | success >> {
    "changed": false,
    "ping": "pong"
}
## Type ansible --help for more arguments

{% endhighlight %}

On the commands above, we use the Ansible module ping  to execute directly on host vagrant@192.168.56.101 . `A module is a command that can be executed directly on remote hosts`. There are many [modules available](http://docs.ansible.com/modules_by_category.html) that allow us to do all kind of tasks such as network, system, etc.


### 5. Playbook


An Ansible playbook is a `collection of deployment configurations` that you can execute on remote hosts. Imagine that, instead of executing one Ansible command after another like in previous section, you can use playbook, define a set of modules in its own syntax to run on remote hosts.


<blockquote>If Ansible modules are the tools in your workshop, playbooks are your design plans.</blockquote>


Example of a simple playbook file named playbook.yml , which is in YAML format:

{% highlight yaml %}
---
- hosts: webservers    # remote hosts to run the playbook
  vars:     # Variables used in the template files
    http_port: 80
    max_clients: 200
  remote_user: vagrant
  sudo: yes    # use sudo when executing command, if remote_user is root, you don't need it
  tasks:       # tasks to run on remote hosts
  - name: ensure apache is at the latest version
    yum: pkg=httpd state=latest       # use module "yum": yum install httpd
  - name: write the apache config file
    template: src=/srv/httpd.j2 dest=/etc/httpd.conf    # copy template file to remote hosts
    notify:      # run after task is executed
    - restart apache    # handler's name
  handlers:     # tasks that would be triggered by notify
    - name: restart apache
      service: name=httpd state=restarted

{% endhighlight %}

Executing a playbook:

~~~
$ ansible-playbook playbook.yml -u vagrant
~~~

You can type `ansible-playbook --help` for more parameters.


### 6. Playbook project structure


You might realize that if we are up to deploy a large group of tools, then put all the configurations into just one file like above is not good. Follow this [link](http://docs.ansible.com/playbooks_roles.html) and you will learn about playbook roles and file include that enable configuring in separate files and reusing them in several projects. Here I will explain the playbook project structure which I'm applying:

~~~
install.yml        # Define hosts, user, roles to deploy
hosts              # List of remote hosts
group_vars/        # Variables used in template files
  all
roles/
   role1/          # role name, used to identify the role
     files/        # files that could be copied to remote hosts
       file1.txt
     templates/    # parameterized templates that could be copied to remote hosts,
       template1.j2
     tasks/        # tasks that will be executed
       main.yml
     handlers/     # tasks triggered by notify
       main.yml
   role2/
     files/
     templates/
     tasks/
     handlers/
~~~

Executing ansible playbook:

~~~
$ ansible-playbook ./install.yml -i ./hosts -vvvv
~~~

### 7. Build up your own playbook!

I have introduced about Ansible commands, modules and playbooks and that all you need to build up an automated deployment using Ansible!

You might wanna checkout my playbook project for ubuntu 12.04 precise:

~~~
$ git clone https://github.com/ntquyen/ansible-ubuntu12.04-environment.git
~~~



##  Integrate Ansible playbook into Vagrant


You've learned how to use Ansible to deploy your servers. In case you are about to deploy your local development environment using Vagrant, it could be more convenient if you use Ansible as the Vagrant's provisioner. It means that after setting up the machine, Vagrant will ask Ansible for deploying all tools configured in its playbook. There's no need to execute the ansible-playbook  command.

All you need to do is declare Ansible provisioner in theVagrantfile like below:

{% highlight ruby %}
config.vm.provision "ansible" do |ansible|
  ansible.playbook = "/path/to/install.yml" #Configure to use which playbook
  ansible.inventory_path = "/path/to/hosts/file" #Configure to use which hosts
  ansible.verbose = 'vvvv'
  ansible.sudo = true
end
{% endhighlight %}


Check out this [link](https://docs.vagrantup.com/v2/provisioning/ansible.html) for more useful configurations with Ansible.

Run the command below to call Ansible playbook with Vagrant:

~~~
$ vagrant provision --provision-with ansible
~~~

That's all! If you have been through all the steps above, I can assure that you will be able to setup and run your own deployment configuration without trouble!


## Troubleshooting


The only thing that troubled me during the setup is the user authentication. So you must pay attention to:

- **The remote user and the current user**: You should explictily declare  the remote user  in the command or playbook to avoid the confusion.

- **The public key**: make sure the public key of the current user is copied to remote machines.

- **The sudo password**: if the remote user needssudo  privilege, you need to declare sudo: yes   in the playbook or `ansible.sudo = true`  inVagrantfile .

- **verbose mode**: execute Ansible in verbose mode by add `-vvvv` in the command or `ansible.verbose = true` in Vagrantfile


## Useful links


One great thing about Vagrant and Ansible is that their documentations are very clear and easy to follow. Below list some useful links that you can take as references:

- Vagrant provisioning with Ansible: [https://docs.vagrantup.com/v2/provisioning/ansible.html](https://docs.vagrantup.com/v2/provisioning/ansible.html)

- Ansible modules: [http://docs.ansible.com/list_of_all_modules.html](http://docs.ansible.com/list_of_all_modules.html)

- Ansible playbook best practices: [http://docs.ansible.com/playbooks_best_practices.html](http://docs.ansible.com/list_of_all_modules.html)

In the future, I'm gonna learn and try to deploy my environment in clouds using Ansible. That seems very interesting stuff to learn!

## Related posts

- [Using Vagrant & Ansible to automate development environment – Part 1]({% post_url 2014-02-25-using-vagrant-ansible-automate-development-environment %})

- [Using Vagrant & Ansible to automate development environment – Part 2]({% post_url 2014-03-05-using-vagrant-ansible-to-automate-development-environment-part-2 %})
