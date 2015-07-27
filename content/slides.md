**Virtualizing Development Environments**

July 27, 2015

---

<span style='font-size:1pt'>mash the 's' key for presenter notes</span>



## Topics

  - Background
    - what I'm developing
  - Development Environment
    - technology used
    - how I'm using it
  - Relevance to others
    - what build artifacts may be useful to others



## Server Lifecycle



![boxed server](content/boxserver.jpg)

Note:
Server comes in a box



![server](content/server.jpg)

Note:
We take server out of the box, put it in a rack



![quantum](content/quantum.jpg)

Note:
We use the box for quantum experiments



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



###  EuPathDB's Puppet Code

- Our manifests date back to 2007, when
  - Puppet was still immature
  - few best practices
  - we were novices
- Increasingly incompatible with each new Puppet version
- Not modular enough to use on new classes of servers
- **Time for a full rewrite**



### Development Project

  - Complete rewrite of our Puppet implementation



### Development Cycle

1. freshly installed OS on bare metal
2. write Puppet manifests
3. apply manifests
4. run functional tests on server
5. go to 1

Note:
what if working on a single application such as  wdk and want to start over? delete gus_home and build from source.

what if what you are working on is the entire operating system. how do you delete and start over? Need sources and a way to build it.



### Need a development environment



### Need development environment

  - Use a virtual machine



#### Traditional virtual machines

  - create VM
  - install OS
  - Snapshot (point in time recovery)
  - do work
  - revert to snapshot

Note:
Workable but cumbersome when you need to start over several times an hour.

__This is not a suitable development environment!__

This is of course a very common problem.  About two years ago a software developer named Mitchell Hashimoto released a software tool called Vagrant that addresses this problem.



<img style="border:none" src="content/vagrantlogo.png">



![](content/vagrantmovie.jpg)



<img style="border:none" src="content/vagrantlogo.png">

<a href='https://www.vagrantup.com' target='_blank'>https://www.vagrantup.com</a>

Note:
Vagrant will be the focal point of the rest of this talk.



### Vagrant

  - acquire VM image (box)
  - start/stop VM
  - reset VM to baseline
  - manage VM characteristics
    - shared folders between host and guest
    - network, memory, CPU
  - manage provisioning



### Vagrant

- Requires Vagrant-compatible VM image
  - Make one yourself
  - Use someone else's
    - <a href="https://atlas.hashicorp.com/boxes/search" target="_blank">https://atlas.hashicorp.com/boxes/search</a>

Note:
- In order to manage a given virtual machine Vagrant has some expectations. For example, in order for it to shut down a VM it has to log in and run the relevant command. So it needs a known user account and that user account needs permissions to shut down the server. 

- There are other requirements. Not important now.

So, going back to what I said earlier - we need a virtual machine - but not any virtual machine - one that is compatible with Vagrant. Where do we get that? Well, we can make one from scratch, the requirements are documented. But it would be less work to use one someone else has already made. **I'm talking about obtaining a base VM that just has a minimal Linux installed. This VM does not need any of EuPathDB's tools and configurations. That stuff will be installed through Puppet.**. 



### My Development Environment

  - <a href="https://www.virtualbox.org/wiki/Downloads" target="_blank">VirtualBox</a>
  - <a href="http://www.vagrantup.com/downloads.html" target="_blank">Vagrant</a>
  - <a href="https://atlas.hashicorp.com/boxes/search" target="_blank">Vagrant compatible VM Image</a> ("box")



## Vagrant Demo

  - mkdir sig-demo
  - https://atlas.hashicorp.com/boxes/search
  - vagrant init puppetlabs/centos-6.6-64-puppet

Note:
make a working directory. Find a suitable box at hashicorp.
vagrant init
show Vagrantfile



## Vagrant Demo

 - vagrant up
 - vagrant ssh
 - /vagrant shared directory
 - vagrant halt
 - vagrant destroy



## Puppet Development Demo

  - VPN
    - <a href='https://wiki.apidb.org/index.php/sshuttle' target='_blank'>sshuttle</a>
  - ~/Vagrant/vagrant-eupathdb-webserver/
  - vagrant up
  - vagrant provision

Note:
  remember to start sshuttle
  Vagrantfile provisions via Puppet
  Puppet manifests are on my laptop and shared with VM
  Tomcat is already installed, /usr/local/tomcat
  emacs is not (Matt and I have not gotten that far with the Puppet rewrite)
  Let's install it
  wdk_templatedb_demo.pp <- include ::ebrc_packages::emacs
  vagrant provision



### Test Provisioned Server

  1. Checkout WDK source code
  2. Build TemplateDB web site
  3. Destroy VM
  4. Continue Puppet development with new VM instance
  5. Go To 1



#### One Man's Trash Is Another Man's Treasure
![Trash](content/trash.jpg)

Note:
In my development cycle I'm throwing away the provisioned VM after I've
evaluated its suitability for installing a website.



### Yard Sale!

  - vagrant package --base sa.apidb.org --output ~/Vagrant/wdk-base.box
  - upload the box to http://software.apidb.org/vagrant/
  - interested parties can create a Vagrantfile and use that box



### Example

<a href='https://github.com/mheiges/vagrant-wdk-templatedb' targe='_blank'>https://github.com/mheiges/vagrant-wdk-templatedb</a>

  - git clone https://github.com/mheiges/vagrant-wdk-templatedb
  - vagrant up
  - <a href='http://127.0.0.1:9380/strategies/' target='_blank'>http://127.0.0.1:9380/strategies/</a>

Note:
be sure sshuttle is running
cp private.yml from existing project
vagrant up, then review playbook.yml while provisioning



### Closing comments

  - If there is interest, I can continue to provide boxes
    - not officially supported but welcome feedback
  - Still at very early stage of Puppet rewrite
    - can at least deploy TemplateDB
  - Never completely free
    - Individuals will want to customize the box for specific needs.
      - start new Vagrant project
      - customize provisioning

Note:
e.g. Ryan will want to tweak it. e.g. Add debugger tools. Reconfigure shared folders. Install PlasmoDB instead of TemplateDB.

Encode those changes in Vagrant manifests and add commit that to Subversion. 

Steve, Dave and Cristina can svn update and `vagrant provision` to get the changes.

Ryan does not need to teach several people how to make the change. He only teaches Vagrant.
