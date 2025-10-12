---

title: "Napping"

---
<center>
<strong>1. 🔍 Enumeration Part + Directory Bruteforcing</strong><br> 
<strong>2. ☠️ Exploitation Part</strong><br>
<strong>3. 🔓 Privilege Escalation Part</strong>
</center>

---

<h2><span style="color:red">1. 🔍 Enumeration Part + Directory Bruteforcing</span></h2><br>
We will start from the nmap scan:
<center>
<img src="./images/nmap_napping.png"> 
</center><br>

Directory bruteforcing using gobuster tool:<br>
```bash
gobuster dir -u http://10.10.136.217/ -w=/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x php
```
<center>
<img src="./images/gobuster_napping.png">
</center><br>

We also see that /admin directory appeared, going further to see more:<br>
```bash
gobuster dir -u http://10.10.136.217/admin/ -w=/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x php
```

<center>
<img src="./images/gobuster_admin_napping.png"> 
</center>

Cheking the main page:<br>
<center>
<img src="./images/mainpage_napping.png">
</center>

We will sign up a new account<br>
<center>
<img src="./images/sign_up_napping.png">
</center><br>

Also checking the admin page on /admin/login.php<br>
<center>
<img src="./images/admin_login_napping.png">
</center>

After creating and signing up as a hacker, we see the text that says "Please submit your link so that we can get started.
All links will be reviewed by our admin who also built this site!"<br>
<center>
<img src="./images/loggedin_napping.png">
</center>
<br>

By cloning the /admin/login.php page and creating a phishing HTML page with credential capture functionality, we can attempt to trick the admin into entering their credentials when they review our submitted link.<br>

<h2><span style="color:red"><strong>2. ☠️ Exploitation Part</strong></span></h2><br>

Let's get our /admin/login.php page:<br>
<center>
<img scr="./images/wget_login_php_napping.png">
</center><br>
And create test.html document for our phishing part:

