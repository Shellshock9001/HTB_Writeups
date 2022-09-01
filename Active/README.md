![Active_call_Card](https://user-images.githubusercontent.com/110210595/187805520-c29bf5fc-ce6e-484b-a14b-33bd87e78c78.png)

# Nmap Scan 
Started off with a nmap. `nmap -Pn -T5 -sV -sC -A -p- -oN active_nmap.txt 10.10.10.100`<br>
Here is the results:
![nmap_scan](https://user-images.githubusercontent.com/110210595/187805488-8fc95761-8751-42fe-a1cf-1533ec9efcd6.png)

Interested port is port 88 which accourding to https://book.hacktricks.xyz/network-services-pentesting/pentesting-kerberos-88 is a kerberos port. Unfortuneately we'll have to come back to this later until we get some credentials. But this is very powerful and will get us a kerberos ticket, hopefully the admins account. 
