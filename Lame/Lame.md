# Lame - 10.10.10.3

Started off with a nmap scan
``` markdown
nmap -Pn -T5 -sV -sC -A -p- -oN lame_nmap.txt 10.10.10.3
```
While that was scanning I went to go check out to see if the website is up. But I don't get anything back and you'll see why when the results are done.
We get back some nice info to start us off.
Port 80 isn't open so there was no site for me to check.\
But there was other ports open such as port 21 with the version number of vsftpd 2.3.4 (This version of vsftpd is vulnerable to backdoor command execution\
CVE-2011-2523) I couldn't get it to work though. So I moved on. If you can't get something to work, don't spend to much time on it. Look at the next route you can take and if you get stuck.\
again then go back and try to repeat your steps to make sure you didn't make a typo somewhere.

