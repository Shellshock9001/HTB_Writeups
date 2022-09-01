![Active_call_Card](https://user-images.githubusercontent.com/110210595/187805520-c29bf5fc-ce6e-484b-a14b-33bd87e78c78.png)

# nmap Scan 
Started off with a nmap. `nmap -Pn -T5 -sV -sC -A -p- -oN active_nmap.txt 10.10.10.100`<br>
Here is the results:

![nmap_scan](https://user-images.githubusercontent.com/110210595/187805488-8fc95761-8751-42fe-a1cf-1533ec9efcd6.png)

We see that smb is open.<br>
I run `smbmap -H 10.10.10.100`<br>

# smbmap
![smbmap_results](https://user-images.githubusercontent.com/110210595/187825021-0c32621b-4d50-41f8-ab62-2bc20b94d9cd.PNG)

We notice the domain name is active.htb. This is great because we'll need this for the kerberos attack. We still need credentials.<br>
Only share we have access to is <em><strong>Replication</em></strong> and its only <em><strong>READ</em></strong> acces.<br>

I use `smbclient \\\\10.10.10.100\\Replication`<br>
I log in without a password and just press `ENTER` to hope anonymous log in is enabled.<br>

![smbclient_Replication_results](https://user-images.githubusercontent.com/110210595/187825380-c02c7cea-bf2e-496c-b255-b63c1b97e355.PNG)

We see <em><strong>ctive.htb</em></strong> again in the list and scour this entire directory. Lot's of directories that are empty, countless `cd ..` and cd directories until we ended up finding an interesting file.<br>

![Groups_xml_finding](https://user-images.githubusercontent.com/110210595/187825500-fd5a0269-be38-40d4-b360-8418a8294e77.PNG)

We find a <em><strong>Groups.xml</em></strong> file.<br>
Used `get Groups.xml`<br>
`cat Groups.xml` didn't work hehe.<br>

![Groups_xml_results](https://user-images.githubusercontent.com/110210595/187825600-fece54cb-5614-46d0-afd7-846d569fc7ff.PNG)
