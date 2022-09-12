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

