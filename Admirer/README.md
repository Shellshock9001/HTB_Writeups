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
and a <!--scripts --> section
<!-- Scripts -->
<script src="assets/js/jquery.min.js"></script>
<script src="assets/js/jquery.poptrox.min.js"></script>
<script src="assets/js/browser.min.js"></script>
<script src="assets/js/breakpoints.min.js"></script>
<script src="assets/js/util.js"></script>
<script src="assets/js/main.js"></script>

after this I go check out the <em><strong>robots.txt</em></strong> file

Don't get much 
User-agent: *
# This folder contains personal contacts and creds, so no one -not even robots- should see it - waldo
Disallow: /admin-dir


