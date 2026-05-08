
## WSTG-INFO-02 — Web Server Fingerprint (Headers & Banners)

This guide outlines techniques for identifying web server technology, versions, and underlying stacks by analyzing HTTP response headers, error pages, banners, and behavioral patterns.

## 🎯 Purpose
The goal is to map the target's technology stack to identify specific vulnerabilities (CVEs) before launching an attack.

## 🚀 Why It Matters
| Discovery | Potential Impact |
| :--- | :--- |
| **Apache 2.4.49** | Search CVE-2021-41773 (Path Traversal) |
| **PHP/7.2.0** | Search known RCE CVEs for that version |
| **X-Powered-By: ASP.NET** | Target .NET-specific attack vectors |
| **Server: IIS/6.0** | Legacy system - high probability of exploit availability |

---

## 🛠 Methodology

### 1. HTTP Response Headers
Analyze the headers returned by the server for versioning and framework details.

```bash
# Basic header grab
curl -I https://target.com

# Follow redirects + verbose mode
curl -Iv https://target.com

# Custom user agent to bypass simple filters
curl -I -A "Mozilla/5.0" https://target.com

# Disable redirect follow
curl -I --max-redirs 0 https://target.com
Headers to look for
Server: Apache/2.4.52 (Ubuntu)        ← server + version + OS
X-Powered-By: PHP/8.1.0               ← backend language
X-Powered-By: ASP.NET                 ← .NET stack
X-AspNet-Version: 4.0.30319           ← .NET version
X-Generator: Drupal 9                 ← CMS
X-Drupal-Cache: HIT
Via: 1.1 varnish                      ← CDN/cache layer
X-Varnish: 12345
X-Cache: HIT from cloudfront         ← AWS CloudFront
CF-Ray: abc123                        ← Cloudflare
X-Served-By: nginx                    ← proxy layer
Set-Cookie: PHPSESSID=...             ← PHP
Set-Cookie: JSESSIONID=...            ← Java/Tomcat
Set-Cookie: ASP.NET_SessionId=...     ← ASP.NET
Set-Cookie: rack.session=...          ← Ruby/Rack

METHOD 2 — Banner Grabbing
Netcat
bashnc -v target.com 80
GET / HTTP/1.0
[press Enter twice]
Telnet
bashtelnet target.com 80
GET / HTTP/1.0
Host: target.com
[Enter twice]
Nmap banner grab
bash# Service version detection
nmap -sV target.com

# Aggressive version + OS
nmap -A target.com

# Specific port
nmap -sV -p 80,443,8080,8443 target.com

# HTTP specific scripts
nmap --script http-headers target.com
nmap --script http-server-header target.com
nmap --script http-generator target.com
nmap --script banner target.com

METHOD 3 — Automated Tools
WhatWeb
bash# Install
sudo apt install whatweb

# Basic scan
whatweb target.com

# Aggressive (more requests)
whatweb -a 3 target.com

# Output to file
whatweb -a 3 target.com --log-json=results.json

# Multiple targets
whatweb -i targets.txt
Nikto
bash# Full scan with fingerprint
nikto -h https://target.com

# Specific port
nikto -h target.com -p 8443

# With auth
nikto -h target.com -id admin:password
Wappalyzer CLI
bashnpm install -g wappalyzer
wappalyzer https://target.com
httprobe + httpx
bash# httpx — fast, rich fingerprint
httpx -u target.com -tech-detect -status-code -title -server -content-type

# Multiple targets
cat hosts.txt | httpx -tech-detect -server -title

METHOD 4 — Error Page Analysis
Trigger error pages — they often leak server info.
bash# 404 page
curl https://target.com/thispagedoesnotexist123

# 405 method not allowed
curl -X DELETE https://target.com/

# 400 bad request
curl -X GET "https://target.com/<script>"

# 500 server error
curl -X POST https://target.com/ -d "'"
Look for signatures in error pages:
Signature in HTMLServerApache Tomcat/X.XJava/TomcatMicrosoft IISWindows/IISnginx/1.XNginxPowered by ExpressNode.jsWhoops! There was an error.Laravel/PHPA PHP Error was encounteredCodeIgniterDjango in tracebackPython DjangoSinatra doesn't knowRuby Sinatra

METHOD 5 — Cookie Analysis
bashcurl -I https://target.com | grep -i "set-cookie"
Cookie NameFrameworkPHPSESSIDPHPJSESSIONIDJava (Tomcat/Spring)ASP.NET_SessionIdASP.NET_rails_sessionRuby on Railslaravel_sessionLaravelci_sessionCodeIgniterwordpress_WordPresswp-settings-WordPresscsrftokenDjango

METHOD 6 — File Extension Probing
bash# Try common extensions to identify backend
curl -o /dev/null -sw "%{http_code}" https://target.com/index.php
curl -o /dev/null -sw "%{http_code}" https://target.com/index.asp
curl -o /dev/null -sw "%{http_code}" https://target.com/index.aspx
curl -o /dev/null -sw "%{http_code}" https://target.com/index.jsp
curl -o /dev/null -sw "%{http_code}" https://target.com/index.cfm
200 or redirect = that backend exists.

METHOD 7 — Favicon Hash (Shodan)
Every framework has a default favicon. Hash it → search Shodan.
bash# Get favicon hash
python3 -c "
import requests, mmh3, codecs
r = requests.get('https://target.com/favicon.ico', verify=False)
favicon = codecs.encode(r.content, 'base64')
hash = mmh3.hash(favicon)
print(hash)
"

# Then search Shodan
shodan search "http.favicon.hash:-XXXXXXX"
Common favicon hashes:
HashProduct-2038522926Grafana116323821Cisco1278530552GitLab-601665621Jenkins999357577Jupyter Notebook

FULL WORKFLOW — INFO-02
1. curl -I target.com              → grab headers
2. whatweb -a 3 target.com         → auto fingerprint
3. nmap -sV -p 80,443 target.com   → version detect
4. Trigger 404/500 → read error page
5. Check cookies → identify framework
6. Probe file extensions → confirm backend
7. Hash favicon → Shodan search
8. Document: Server + Version + OS + Framework + CDN

DOCUMENT FINDINGS
FindingRiskNext TestServer version exposedMEDIUMSearch CVEs for that versionPHP/ASP version in headersMEDIUMINPV-05, INPV-11Stack trace in error pageHIGHERRH-01Old server version (EOL)CRITICALCONF-01, exploit CVEsCDN detected (Cloudflare)INFOBypass → find real IP

DEFENSE (what good server looks like)
Server: (blank or generic)          ✅
X-Powered-By: (removed)             ✅
Errors: custom 404/500 pages        ✅
Cookies: generic names              ✅
Headers: security headers present   ✅
