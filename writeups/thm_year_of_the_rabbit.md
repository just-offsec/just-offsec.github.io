---

title: "Year of the Rabbit"

---

<center>
<strong>1. 🔍 Enumeration Part + Directory Bruteforcing</strong><br> 
<strong>2. ☠️ Exploitation Part</strong><br>
<strong>3. 🔓 Privilege Escalation Part</strong>
</center>

---



<h2><span style="color:red">1. 🔍 Enumeration Part + Directory Bruteforcing</span></h2><br>


We start with an Nmap scan:<br>
<center>
<img src="./images/year_of_the_rabbit/1.png"> 
</center><br><br>


Next, we perform directory bruteforcing using Gobuster:<br>
```bash
gobuster dir -u http://10.10.218.46/ -w=/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```
<center>
<img src="./images/year_of_the_rabbit/2.png">
</center><br><br>


The scan reveals an /assets directory. Let's explore it further:<br>
```bash
gobuster dir -u http://10.10.136.217/asset/ -w=/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x php
```

<center>
<img src="./images/year_of_the_rabbit/3.png">
</center><br><br>


We check the "style.css" file, let's see what's hidden there:<br>
<center>
<img src="./images/year_of_the_rabbit/4.png">
</center><br><br>


Hint tells us to go and check "/sup3r_s3cr3t_fl4g.php":<br>
<center>
<img src="./images/year_of_the_rabbit/5.png">
</center><br>



Here you can notice some redirection moves and the browser tells us to turn off javascript:<br>
<center>
<img src="./images/year_of_the_rabbit/6.png">
</center><br><br>


To turn JS off for Firefox browser just follow my guide:<br>
<center>
<img src="./images/year_of_the_rabbit/7.png"><br><br>
<img src="./images/year_of_the_rabbit/8.png"><br>
</center><br>
Done<br>

Finally we can open the page, as we already noticed page has no more .php extension<br>
<img src="./images/year_of_the_rabbit/9.png"><br>
<center>
<img src="./images/year_of_the_rabbit/10.png">
</center><br><br>


YOU HAVE TO WATCH THE VIDEO :))) it will give you the hint for future steps:<br><br>


We can notice weird burp sound from the guy in the video<br>
which says go to the BURPSUITE)), and also<br>
notice some reddirect moves when we open our .php file<br>
let's check it with BURPSUITE:<br>


<img src="./images/year_of_the_rabbit/11.png"><br>
 
<img src="./images/year_of_the_rabbit/12.png"><br>
We found a new directory, open it:<br>
<center>
<img src="./images/year_of_the_rabbit/13.png">
</center><br><br>


<h2><span style="color:red"><strong>2. ☠️ Exploitation Part</strong></span></h2><br><br>


We found one picture where says Hot_babe xD<br> 
Let's get that image and dig further:<br>

<img src="./images/year_of_the_rabbit/14.png"><br><br>


With the comand
```bash
binwalk -e Hot_babe.png
```
extracting some info :<br>

<img src="./images/year_of_the_rabbit/15.png"><br>

<img src="./images/year_of_the_rabbit/16.png"><br>

<img src="./images/year_of_the_rabbit/17.png"><br><br><br>


With command strings extracting all metadata from the file:
```bash
strings 
```
<img src="./images/year_of_the_rabbit/18.png"><br>
Here we have a message of possible correct password:<br>
<img src="./images/year_of_the_rabbit/19.png"><br><br>

Use hydra to bruteforce this passwords for FTP using:<br>
<center>
<img src="./images/year_of_the_rabbit/20.png"><br>
</center><br>

Connect to the FTP server and get file Eli's_Creds.txt:<br>
<center>
<img src="./images/year_of_the_rabbit/21.png"><br>
</center><br>

Trying to gues what it can be:
<center>
<img src="./images/year_of_the_rabbit/22.png">
</center><br>


Eli's_Creds.txt shows us some symbols, simple GPT short message will tell us what are we dealing with:<br>
<center>
<img src="./images/year_of_the_rabbit/23.png">
<img src="./images/year_of_the_rabbit/24.png">
</center><br>

Finding online tool for decode and get our new creds<br>
<img src="./images/year_of_the_rabbit/25.png">
<img src="./images/year_of_the_rabbit/26.png"><br><br>


Lets ssh to the system:<br>
<center>
<img src="./images/year_of_the_rabbit/27.png">
</center><br>

And check the message:<br>
<center>
<img src="./images/year_of_the_rabbit/28.png">
</center><br><br>


<h2><span style="color:red">3. 🔓 Privilege Escalation Part</span></h2><br>


We found creds of Login as a gwendoline<br> 
<center>
<img src="./images/year_of_the_rabbit/29.png">
</center><br>


Use
```bash
sudo -V
```
```bash
sudo -l
```
commands to check possibility for privesc:<br>
<center>
<img src="./images/year_of_the_rabbit/30.png"><br>
<img src="./images/year_of_the_rabbit/31.png">
</center><br><br>

NOTE: sudo -l give us (ALL, !root)NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt<br>
It means that vi can be used to make changes as a root user to the user.txt file<br><br>

 
Ok, lets dig what technik we can use with that sudo version:<br>
<img src="./images/year_of_the_rabbit/32.png">
<img src="./images/year_of_the_rabbit/33.png"><br><br>


NOTE: We have the same example on the image but different command which we can run (in our case it's /usr/bin/vi /home/gwendoline/user.txt)
So what we must do: 
```bash
sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt
```
<br>
Once file opened and below when we save it and giving a name, we write:
```bash
:!bin/sh
```
<br>
<img src="./images/year_of_the_rabbit/34.png">
<br><br>

 
<center>
<img src="./images/year_of_the_rabbit/35.png">
</center><br><br>


<center>
We are root!
</center>
