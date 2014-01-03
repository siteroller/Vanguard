#Detailed Playbooks

A set of playbooks, detailed enough to be used by beginners and noobs, complete enough to be used in production.

##Background
We use Ansible to manage our servers and Vagrant to ensure our developers have a common work environment.  
The scripts herein are designed to make both of these tasks painless and consistent.   
The documentation helps me keep track of why we do things, as well as hopefully enabling others to get started using some wonderful tools.

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
SSH is a secure way for one computer to connect to another, and Ansible uses it heavily.  
Computer #1 has a private key which is never given out. Computer #2 is given a matching public key.   
	The public key can be calculated from the private key, and can be given out freely. The private key cannot be calculated from the public key, which keeps it, well, private. One can safely use the same private key in many sites, and needn't worry that one of the sites will gain his credentials.
	When #1 attempts to connect to #2, the keys are checked for a match. If no match is found, the connection is aborted.  

	In non-Windows machines SSH is built into the command line.   
	In Windows, use Git Bash (included with msysgit), a command line with SSH and other Unix tools built in. For ease of use, add a shortcut on your desktop to git bash. (For Vagrant users, edit the base folder in the shortcut properties).  
	For other programs (such as [Heidi]), you may also need [Pageant][Putty] running. While not needed for these playbooks, the setup is as follows:
	- Run the steps described in "Getting Statrted:SSH" to generate a key in Git Bash.
	- Open [PuTTYgen][Putty]. Load in the just created existing id_rsa file.
	- Save the private key as id_rsa.ppk. This creates a "putty ppk".
	- Open Pageant (in system tray after opening) and add key -> id_rsa.ppk.
4. **Is this safe to use?**  
Everything included is industry standard.  
The software included here is almost always way ahead of their propietary competitors'. Nonetheless, there is no warranty. Use at your own risk.


## Getting Started.
Ansible runs over SSH, even when operating on the same computer it is installed on. It is therefore easy to administer a remote server while only running Ansible on your local machine.  
Nonetheless, for consistency, we assume that you are either seting up Vagrant locally, or using SSH to connect to a remote computer, and installing Ansible there. 
All commands are meant to be run on the computer with Ansible. In the case of a VPS, that means using SSH to connect, and running there.
Furthermore, since most users will be running this as their first steps, it is assumed you are running as rooot. If not, you will often have to run sudo.

1. Install git: `apt-get install git`
	- For Vagrant, install git on the local computer, since Ansible will run locally during virtual machine setup.
	- For Windows users, install git on the local computer in order to have use of Git Bash (see "What is SSH?" above).
2. Install Ansible (or Vagrant, which includes Ansible).
 	- `apt-get install ansible`
 	- If you are using Vagrant, install [VirtualBox] and [Vagrant]. 
3. Setup SSH (done automatically by Vagrant).  
	Ansible uses SSH to run, even when running on localhost. Your setup must allow you to connect to yourself over SSH, as though you were both the requesting and receiving computers.  
	Setup is as follows (in Git Bash on Windows, or the command line anywhere else):
	- Create the private key (id_rsa) and public key (id_rsa.pub) in the folder ~/.ssh/  
 	`ssh-keygen -t rsa -C "your_email@example.com"`
	- Add the private key to your ssh agent:
 	`ssh-add id_rsa`  
 	(If you get an err, ssh-add is not running. `exec ssh-agent bash` & retry)
 	- All "accepted" keys are kept in a file called authorized_keys. Copy id_rsa.pub into it.  
 	`cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`
 	- Check that the owner and permissions are correct. SSH will NOT work if permissions are wrong or too permissive!
 		- `ls -ld ~/.ssh` Should be: `drwx------  00 file_owner ..`
 		- `ls -l ~/.ssh/id_rsa` Should be: `-rw------- 00 file_owner ..`
 		- If owner is wrong, set manually or with whoami: 
 			- `sudo chown $(whoami) -R file_or_folder`  
 		- If permissions are wrong, set them: 
 			- `sudo chmod 700 ~/.ssh`
 			- `sudo chmod 600 ~/.ssh/id_rsa` 
4. Not related to Ansible, but if you are using a VPS, it is highly recommended that you change the default password.
	- `passwd`
5. Clone in the playbooks we will be using.
	- `git clone https://` 
	- I encourage you to fork our repo and start working on it. We are interested in feedback and commits, even if you are new to the field.

[Heidi]: http://www.heidisql.com/download.php
[Putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[VirtualBox]: https://www.virtualbox.org/wiki/Downloads
[Vagrant]: http://www.vagrantup.com/downloads.html

## Playbooks
Instructions are given to Ansible through what are referred to as playbooks.
The following playbooks are included.

1. Create a user for Ansible, called "deploy".
2. Include common packages such as etckeeper, .
3. Setup apt to update itself automatically.
4. Install Nginx and PHP and allow you to test them.
5. Install Mysql and Postgres and secure them. 
6. Install Postfix and test send a mail.

##Running your playbooks:
1. We have setup many playbooks, but for beginners, it is recommended to run only one at a time using "tags".
	- (It is possible, if needed to end a playbook in the middle using fail module or failed_when.) 
2. Call the hosts [default host, hosts file, and 'localhost,'] call the verbose debug.



