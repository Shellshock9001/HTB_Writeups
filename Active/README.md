![Active_call_Card](https://user-images.githubusercontent.com/110210595/187805520-c29bf5fc-ce6e-484b-a14b-33bd87e78c78.png)

# nmap Scan 
Started off with a nmap. `nmap -Pn -T5 -sV -sC -A -p- -oN active_nmap.txt 10.10.10.100`<br>
Here is the results:

![nmap_scan](https://user-images.githubusercontent.com/110210595/187805488-8fc95761-8751-42fe-a1cf-1533ec9efcd6.png)

We see that smb is open.<br>
I run 'smbmap -H 10.10.10.100'<br>

# smbmap
![smbmap_results](https://user-images.githubusercontent.com/110210595/187825021-0c32621b-4d50-41f8-ab62-2bc20b94d9cd.PNG)

We notice the domain name is active.htb. This is great because we'll need this for the kerberos attack. We still need credentials.<br>
Only share we have access to is <em><strong>Replication</em></strong> and its only <em><strong>READ</em></strong> acces.<br>
