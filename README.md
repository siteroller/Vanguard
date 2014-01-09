#Vanguard 
#Detailed Playbooks For Everyone

**Documented for beginners, complete enough for production.  
The simplest, quickest, and most secure way to setup a server!!**

##Background
We use Ansible to manage our servers and Vagrant to ensure our developers have a common work environment. The scripts herein are designed to make both of these tasks painless and consistent.   
The documentation helps us keep track of why we do things as well as enabling others to use some wonderful tools.

##Includes
- Nginx
- PHP-FPM
- Mysql
- Postgresql
- Git
- etckeeper
- Postfix
- Dovecot
- Roundcube
- ffmpeg
- Change default SSH port
- fail2ban
- Gitlab
- Openfire
- Piwik

##FAQ:
1. **What is Ansible?**  
A way to automate and run common server tasks.  
It is designed to run these tasks on multiple machines.  
The "playbook" is the human readable set of instructions that Ansible follows.  
It is an absolute joy to use.

2. **What is Vagrant?**  
	*You do not need Vagrant in order to use the playbooks in this repository. We make references to it only as far as we feel it useful, or as far as it mimics our own usage.*

	It is common (and recommended) to have a test environment on your computer that mimics the production environment on the server.
One way of keeping the local environment streamlined and consistant with the production server, is to install a "virtual machine".
This virtual environment can run Linux (even if your computer runs Windows or OSX) and be otherwise setup as the production server.
It also allows you to maintain different setups, such as Nginx and Apache, without conflicts.

	However, running in a virtual environmet has it's own challenges, such as not having the tools you may expect from your local environment.
Vagrant blurs the line between the virtual and non-virtual space. It sets up a virtual environment which outputs the data (eg. a webpage) 
as would be in production, while allowing you to access and edit your files locally. 
(Primarrily done by forwarding ports on the virtual machie, and by syncing your local and virtual files.)

3. **What is SSH?**  
SSH is a secure way for one computer to connect to another, and Ansible uses it heavily. On Mac & Linux SSH is built into the command line. On Windows, it's part of Git Bash (included with msysgit).  
Computer #1 has a private key which is never given out. A matching "public" key [calculated from the private key] is given to Computer #2 and any other sites that need to identify you. (One can safely use the same keys for many locations, as there is no way to get the private key from the public one.) When #1 attempts to connect to #2, the keys are checked for a match. If no match is found, the connection is aborted.   
	Vanguard sets up SSH for you, but you should definitely take the time to understand how it works!
	
4. **Is this safe to use?**  
Everything included is industry standard.  
The software included here is almost always way ahead of their propietary competitors'. Nonetheless, there is no warranty. Use at your own risk.


## Setting up your HQ.
Ansible is installed once - on the "control computer" only - and than uses SSH to connect to and manipulate other servers.     
Throughout our docs, we assume that **HQ** is your local computer and **SERVER** is either a remote production server or a (Vagrant) virtual host that is being administered.  We will also discuss running Ansible directly on the computer being adminstered.  
**Unless otherwise noted, all commands are to be run in HQ's terminal [or Git Bash on Windows], and all playbooks will affect SERVER.**  

1. Install git
	- Linux: `sudo apt-get install git`
	- OSX: `brew install git` (If you don't have [brew] yet, now's a good time to get it.)
	- Windows: Install [msysgit]. Even if you will run Ansible directly on the SERVER, you should still install git on the local computer to have use of Git Bash (see "What is SSH?" above).
2. Clone in the playbooks we will be using
	- `git clone https://github.com/siteroller/Vanguard.git` 
	- I encourage you to fork our repo and start working on it. We want feedback and pull requests, even if you are new to the field.
3. [Install] Ansible
 	- Linux: (Using apt or yum)
 		- `sudo add-apt-repository ppa:rquillo/ansible`
 		- `sudo apt-get update`
 		- `sudo apt-get install ansible`
 	- Windows (Git Bash prompt): 
 		- `sudo easy_install pip`
 		- `sudo pip install ansible` 
 	- Mac: `brew install ansible` or use `pip` as in Windows.
 	- For Vagrant users: Ansible is included with Vagrant and used for box setup. See #5.
4. Setup the hosts file (list of SERVERS controlled by Ansible)
	- Open the file /etc/ansible/hosts. If the file or the folder doesn't exist [eg. on a Mac] create them.
	- Add SERVERS, one per line. Prepend nicknames in brackets. Set localhost connection to local, as follows (no need for SSH when editing locally):
		
			localhost     ansible_connection=local
			
			[SERVER]
			12.345.67.890
5. To use Vagrant:
	- Install [VirtualBox] and [Vagrant].
	- Choose the active directory in Vagrant (usually your webroot).  
		- In Mac and Linux, open a terminal and `cd` to the desired location.
		- In Windows, create a shortcut to Git Bash. Right click shortcut to edit properties. Set the path to your root. Double click to open terminal pointing to the root.
	- Init an OS. The "suggested" is Ubuntu 12.04, but you can choose any from [vagrantbox.es](http://vagrantbox.es):  
		- `vagrant init precise32 http://files.vagrantup.com/precise32.box`
	- A file "Vagrantfile" has been added to your root. Edit it to run the ansible file upon startup:

			Vagrant.configure("2") do |config|
				config.vm.box = "precise32"
				config.vm.provision "ansible" do |ansible|
					ansible.playbook = "vanguard_detailed_playbooks/vagrant.yml"
				end
			end
	- Start Vagrant: `vagrant up`. 
	- Test that everything is working: http://localhost:2222
	
[Heidi]: http://www.heidisql.com/download.php
[Putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[VirtualBox]: https://www.virtualbox.org/wiki/Downloads
[Vagrant]: http://www.vagrantup.com/downloads.html
[msysgit]: https://code.google.com/p/msysgit/downloads/list
[brew]: http://brew.sh/
[Install]: http://docs.ansible.com/intro_installation.html
## Playbooks
Instructions are given to Ansible through what are referred to as playbooks.
The following playbooks are included.

1. Create a user for Ansible, called "deploy".
2. Include common packages such as etckeeper, .
3. Setup apt to update itself automatically.
4. Lock down the SERVER (permissions, firewall, etc).
4. Install Nginx and PHP and allow you to test them.
5. Install Mysql and Postgres and secure them. 
6. Install Postfix and test send a mail.

##Running your playbooks:
1. We have setup many playbooks, but for beginners, it is recommended to run only one at a time using "tags".
	- (It is possible, if needed to end a playbook in the middle using fail module or failed_when.) 
2. Call the hosts [default host, hosts file, and 'localhost,'] call the verbose debug.
3. If you have rented a VPS, you probably have a username/password to login with.  
	Vanguard will disable that and generate two users for you - one for your own use and for Ansible. The first time you login, you will need that username and password (change server to whatever nickname you gave your server in the hosts file):
	- `ansible SERVER --ask-pass vps.yaml`
	




## Troubleshooting

###Mac:
1. If you wish to connect locally over SSH on a Mac, don't forget to enable the SSH server: System Preferences -> Sharing -> Remote Login

###Windows:

