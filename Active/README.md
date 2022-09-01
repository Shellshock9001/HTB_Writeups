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

# smbclient
I use `smbclient \\\\10.10.10.100\\Replication`<br>
I log in without a password and just press `ENTER` to hope anonymous log in is enabled.<br>

![smbclient_Replication_results](https://user-images.githubusercontent.com/110210595/187825380-c02c7cea-bf2e-496c-b255-b63c1b97e355.PNG)

We see <em><strong>ctive.htb</em></strong> again in the list and scour this entire directory. Lot's of directories that are empty, countless `cd ..` and cd directories until we ended up finding an interesting file.<br>

![Groups_xml_finding](https://user-images.githubusercontent.com/110210595/187825500-fd5a0269-be38-40d4-b360-8418a8294e77.PNG)

We find a <em><strong>Groups.xml</em></strong> file.<br>
Used `get Groups.xml`<br>
`cat Groups.xml` didn't work hehe.<br>

![Groups_xml_results](https://user-images.githubusercontent.com/110210595/187825600-fece54cb-5614-46d0-afd7-846d569fc7ff.PNG)


We could see<br>
<em><strong>cpassword</em></strong> = "edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ"
<em><strong>userName</em></strong> = active.htb\SVC_TGS<br>
Looks like we definitely found some credentials. But I'm not sure what format that cpassword is. I go to https://book.hacktricks.xyz/welcome/readme and do some research. Looks like cpassword is used in a Groups.xml file which is what we found. Didn't see much else so I used the same site https://book.hacktricks.xyz/welcome/readme but this time searched for Groups.xml file and we find much more info on it. Looks to be a cached GPP Password. 
https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation?q=Groups.xml#cached-gpp-pasword<br>


I use `gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ`<br>

![gpp_decrypt_results](https://user-images.githubusercontent.com/110210595/187825800-edb19a2c-1798-41a7-9866-1c7489bd0fc3.PNG)

The password is <em><strong>GPPstillStandingStrong2k18</em></strong><br>

Now we have -<br> 
UserName = <em><strong>active.htb\SVC_TGS</em></strong><br>
Password = <em><strong>GPPstillStandingStrong2k18</em></strong><br>

# Backtrack - smbclient Users
I remember there were other Users in the smbmap and saw <em><strong>Users</em></strong>. I went back to https://book.hacktricks.xyz/welcome/readme and typed in smbclient users and found a "List shared folders" sections. You can log in to the smbclient using a user parameter if you know the password. Which we do.<br>
That section was actually only to list the shared files. Not connect. Scroll down a bit to Connect/List a shared folder. You'll see this is how to connect. I left the password out because I kept getting errors with it.<br>

![smbclient_Users_errors](https://user-images.githubusercontent.com/110210595/187826348-922411ac-76f4-4b5a-a3e4-53b9c19f6c99.PNG)

I used `smbclient -U SVC_TGS \\\\10.10.10.100\\Users`<br>

![smbclient_Users_SVC_TGS_results](https://user-images.githubusercontent.com/110210595/187826416-57f0c5b8-729a-4d77-a917-02afde46da56.PNG)

We see the <em><strong>SVC_TGS</em></strong> directory and `cd SVC_TGS` and `cd Desktop`<br> 
We see the <em><strong>user.txt</em></strong> file.<br>



