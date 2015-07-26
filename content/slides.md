**Virtualizing Development Environments**

July 2015

---

## Topics

  - Background
    - what I'm developing
  - Development Environment
    - technology used
    - how I'm using it
  - Relevance to others
    - what build artifacts may be useful to others



## No Docker



## Server Provisioning

  - Install Linux OS
  - Run Puppet (see PuppetLabs.com)



## Server Provisioning

  - Puppet will
    - add users and groups
    - install software
    - create directory structures
    - mount storage volumes
    - (un)secure file permissions
    - manage firewall
    - manage application configurations

Note:
Puppet is analogous to Reflow. Instead of being a framework for preparing a database and flat files it prepares computer systems. We write Puppet manifests that describe the desired end state of the server (i.e. Puppet uses a declarative syntax) and some of the code required to achieve that state. Puppet + our manifests/code is analogous to the EuPathDB workflow. Although modular parts could be of general interest, the working implementation is specific for EuPathDB.


## Server Provisioning

  - Run Functional Tests
  - Release

Note:
With Puppet (and a few other automations), bare metal server to release in 4 hours, down from two to three weeks without puppet.




## Puppet rewrite

- Our implementation dates back to 2007, when
  - Puppet was very immature, Puppet version 0.23
  - few best practices
  - we were novices
- Duct tape and string patching over the years
- Difficult to use on ad hoc servers
- **Time for a full rewrite**

Notes:



### Need development environment

- Too risky and complex to use an active server
- To adequately test and debug, we need to wipe and redo.
- Use a virtual machine

what if working on a single application such as  wdk and want to start over? delete gus_home and build from source.

what if what you are working on is the entire operating system. how do you delete and start over? Need sources and a way to build it.

VirtualMachine snapshots.

Workable but cumbersome when you need to start over several times an hour.

This is of course a very common problem.  About two years ago a developer named Mitchell Hashimoto released a software tool called Vagrant that addresses this problem.

Vagrant will be the focal point of the rest of this talk.



### Tools

I need software to host virtual machines. Could be VMWare but I'm using VirtualBox.

We need a disk image that represents the virtual machine. I'll come back to this.

We need software to manage the lifecycle of the virtual machine. What is the lifecycle? For now, let's just say it includes starting and stopping a virtual machine. That's Vagrant.

  - network interfaces
  - memory
  - CPU count

In order to manage a given virtual machine Vagrant has some expectations. For example, in order for it to shut down a VM it has to log in and run the relevant command. So it needs a known user account and that user account needs permissions to shut down the server. 

There are other requirements. Not important now.

So, going back to what I said earlier - we need a virtual machine - but not any virtual machine - one that is compatible with Vagrant. Where do we get that? Well, we can make one from scratch, the requirements are documented. But it would be less work to use one someone else has already made. **I'm talking about obtaining a base VM that just has a minimal Linux installed. This VM does not need any of EuPathDB's tools and configurations. That stuff will be installed through Puppet.**. 

https://atlas.hashicorp.com/boxes/search




#### One Man's Trash Is Another Man's Treasure
![Trash](content/trash.jpg)



https://github.com/mheiges/vagrant-wdk-templatedb

be sure sshuttle is running

vagrant up

127.0.0.1:9380/strategies/


Not completely free.

Ryan will want to tweak it. Add debugger tools. Reconfigure shared folders. Install PlasmoDB instead of TemplateDB.

Be he can encode those changes in Vagrant manifests and add commit that to Subversion. 

Steve, Dave and Cristina can svn update and `vagrant provision` to get the changes.


Ryan does not need to teach several people how to make the change. He only teaches Vagrant.


"Infrastructure as Code"





