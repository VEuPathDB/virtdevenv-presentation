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



## Server Lifecycle



![boxed server](content/boxserver.jpg)

Note:
Server comes in a box



![server](content/server.jpg)

Note:
We take server out of the box, put it in a rack



![quantum](content/quantum.jpg)

Note:
We use the box for quantum experiment



### "Bare metal"

![server](content/server.jpg)

Note:
A computer w/o OS and software is called 'bare metal'



## Provisioning



#### Provisioning

  - Install Linux OS



#### Provisioning

  - add users and groups
  - install software
  - create directory structures
  - mount storage volumes
  - fix file permissions
  - manage firewall
  - manage application configurations



#### Provisioning

  - Run Functional Tests
  - Release



#### Provisioning (and ongoing maintenance)

  - add users and groups
  - install software
  - create directory structures
  - mount storage volumes
  - fix file permissions
  - manage firewall
  - manage application configurations

Note:
Doing these steps manually is time-consuming and error prone.
There are several tools to help automate this process.
We use Puppet.



## Puppet

<img style="border:none" src="content/puppet.png">

Note:
Basic flow. Servers get state definitions from central Puppetmaster.
Manifests that define the state definitions are versioned in source control.




### Example Puppet Manifests

A class that defines a software package to install.

    class ebrc_packages::emacs {
      package { 'emacs' :
        ensure => installed,
      }
    }

Desired classes are included on a node.

    node 'spruce.pcbi.upenn.edu' {
      include ebrc_packages::emacs

Note:
This class installs emacs. 
Classes can be a few lines like this, or can be hundreds of lines.
The class is included in a node manifest.



#### cf. Reflow

- _load genomic data into databases, flatfiles_ 

vs

- _load software, configurations onto an OS_

---

- Workflow datasets, configurations are EuPathDB specific implementations.

- Puppet manifests define EuPathDB specific implementations.

Note:
Puppet is somewhat analogous to Reflow. Instead of being a framework for preparing a database and flat files it prepares computer systems. We write Puppet manifests that describe the desired end state of the server (i.e. Puppet uses a declarative syntax) and some of the code required to achieve that state. Puppet + our manifests/code is analogous to the EuPathDB workflow. Although modular parts could be of general interest, the working implementation is specific for EuPathDB.



With Puppet (and a few other tools):

- Bare metal server to release in 4 hours.
- Down from two to three weeks without puppet.



## EuPathDB's Puppet Code

- Our implementation dates back to 2007, when
  - Puppet was very immature, Puppet version 0.23
  - few best practices
  - we were novices
- Duct tape and string patching over the years
- Difficult to use on new classes of servers
- **Time for a full rewrite**

Note:



#### Need development environment

  - Too risky and complex to use an active server
  - To adequately test and debug, we need to wipe and redo.
  - Use a virtual machine

Note:
what if working on a single application such as  wdk and want to start over? delete gus_home and build from source.

what if what you are working on is the entire operating system. how do you delete and start over? Need sources and a way to build it.

VirtualMachine snapshots.

Workable but cumbersome when you need to start over several times an hour.

This is of course a very common problem.  About two years ago a developer named Mitchell Hashimoto released a software tool called Vagrant that addresses this problem.

Vagrant will be the focal point of the rest of this talk.



### Tools

I need software to host virtual machines.

Could be VMWare but I'm using VirtualBox.



### Tools

We need a disk image that represents the virtual machine.

I'll come back to this.



### Tools

We need software to manage the lifecycle of the virtual machine.

  - start/stop VM
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


Note:
Not completely free.

Ryan will want to tweak it. Add debugger tools. Reconfigure shared folders. Install PlasmoDB instead of TemplateDB.

Be he can encode those changes in Vagrant manifests and add commit that to Subversion. 

Steve, Dave and Cristina can svn update and `vagrant provision` to get the changes.


Ryan does not need to teach several people how to make the change. He only teaches Vagrant.


"Infrastructure as Code"





