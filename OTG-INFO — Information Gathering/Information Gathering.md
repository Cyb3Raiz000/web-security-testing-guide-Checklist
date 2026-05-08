## EXPOSED FILES
```
site:target.com filetype:env
site:target.com filetype:log
site:target.com filetype:sql
site:target.com filetype:bak
site:target.com ext:conf OR ext:config OR ext:cfg
site:target.com ext:xml intext:password
```

## ADMIN PANELS
```
site:target.com inurl:admin
site:target.com inurl:login
site:target.com intitle:"admin panel"
site:target.com inurl:dashboard
site:target.com inurl:wp-admin
```

## DIRECTORY LISTING
```
site:target.com intitle:"index of /"
site:target.com intitle:"index of" "parent directory"
site:target.com intitle:"index of" passwd
site:target.com intitle:"index of" ".git"
```

## SENSITIVE DATA
```
site:target.com intext:"api_key"
site:target.com intext:"secret_key"
site:target.com intext:"Authorization: Bearer"
site:target.com filetype:xls intext:password
site:target.com filetype:pdf "confidential"
```

## ERROR PAGES (leak tech stack)
```
site:target.com intext:"sql syntax near"
site:target.com intext:"Warning: mysql_fetch"
site:target.com intext:"ORA-01756"
site:target.com intext:"Microsoft OLE DB"
site:target.com intext:"Stack trace:"
```

## SUBDOMAINS
```
site:*.target.com
site:*.*.target.com
```

## LOGIN PAGES
```
site:target.com inurl:login filetype:php
site:target.com intitle:"Please Login"
```

## CAMERA / IoT
```
intitle:"webcamXP 5" site:target.com
inurl:"/view/index.shtml" site:target.com
```

## WORKFLOW INFO

```
1. Google dork site:target.com → map surface
2. Find subdomains → site:*.target.com
3. Find exposed files → filetype:env/log/sql/bak
4. Find error pages → intext:"SQL syntax"
5. Shodan → hostname:target.com → all open ports
6. Shodan → ssl:"target.com" → hidden subdomains
7. Check GHDB for target-specific dorks
8. Log everything → feed into CONF/ATHN tests
```


## WSTG-INFO-02 — Web Server Fingerprint (Headers & Banners)

### What Is It?
Identify the web server technology, version, and underlying stack by analyzing HTTP response headers, error pages, banners, and behavioral patterns. Goal: know what you're attacking before you attack it.

### WHY IT MATTERS
FindWhat attacker does with itApache 2.4.49Search CVE-2021-41773 (path traversal)PHP/7.2.0Search known RCE CVEs for that versionX-Powered-By: ASP.NETTarget .NET-specific attacksServer: IIS/6.0Ancient — high chance of exploits

METHOD 1 — HTTP Response Headers

Manual curl
# Basic header grab
```
curl -I https://target.com
```

# Follow redirects + verbose
```
curl -Iv https://target.com
```

# All headers including response body
```
curl -v https://target.com 2>&1 | head -50
```

# Custom user agent
```
curl -I -A "Mozilla/5.0" https://target.com
```

# Disable redirect follow
```
curl -I --max-redirs 0 https://target.com
```
<p>
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
</p>

METHOD 2 — Banner Grabbing
Netcat
```
nc -v target.com 80
```

Telnet
```
telnet target.com 80
```

Nmap banner grab
# Service version detection
```
nmap -sV target.com
```

# Aggressive version + OS
```
nmap -A target.com
```

# Specific port
```
nmap -sV -p 80,443,8080,8443 target.com
```

# HTTP specific scripts
```
nmap --script http-headers target.com
nmap --script http-server-header target.com
nmap --script http-generator target.com
nmap --script banner target.com
```

METHOD 3 — Automated Tools
WhatWeb
# Install

```
sudo apt install whatweb
```

# Basic scan
```
whatweb target.com
```

# Aggressive (more requests)
```
whatweb -a 3 target.com
```

# Output to file
```
whatweb -a 3 target.com --log-json=results.json
```

# Multiple targets
```
whatweb -i targets.txt
```

Nikto
# Full scan with fingerprint
```
nikto -h https://target.com
```

# Specific port
```
nikto -h target.com -p 8443
```

# With auth
```
nikto -h target.com -id admin:password
```

Wappalyzer CLI
```
npm install -g wappalyzer
wappalyzer https://target.com
```

httprobe + httpx
# httpx — fast, rich fingerprint
```
httpx -u target.com -tech-detect -status-code -title -server -content-type
```

# Multiple targets
```
cat hosts.txt | httpx -tech-detect -server -title
```

METHOD 4 — Error Page Analysis
Trigger error pages — they often leak server info.
# 404 page
```
curl https://target.com/thispagedoesnotexist123
```

# 405 method not allowed
```
curl -X DELETE https://target.com/
```

# 400 bad request
```
curl -X GET "https://target.com/<script>"
```

# 500 server error
```
curl -X POST https://target.com/ -d "'"
```

<p >Look for signatures in error pages:
Signature in HTMLServerApache Tomcat/X.XJava/TomcatMicrosoft IISWindows/IISnginx/1.XNginxPowered by ExpressNode.jsWhoops! There was an error.Laravel/PHPA PHP Error was encounteredCodeIgniterDjango in tracebackPython DjangoSinatra doesn't knowRuby Sinatra
</p>

METHOD 5 — Cookie Analysis
```
curl -I https://target.com | grep -i "set-cookie"
```
<p>
  Cookie NameFrameworkPHPSESSIDPHPJSESSIONIDJava (Tomcat/Spring)ASP.NET_SessionIdASP.NET_rails_sessionRuby on Railslaravel_sessionLaravelci_sessionCodeIgniterwordpress_WordPresswp-settings-WordPresscsrftokenDjango
</p>

METHOD 6 — File Extension Probing
# Try common extensions to identify backend
```
curl -o /dev/null -sw "%{http_code}" https://target.com/index.php
curl -o /dev/null -sw "%{http_code}" https://target.com/index.asp
curl -o /dev/null -sw "%{http_code}" https://target.com/index.aspx
curl -o /dev/null -sw "%{http_code}" https://target.com/index.jsp
curl -o /dev/null -sw "%{http_code}" https://target.com/index.cfm
```
200 or redirect = that backend exists.

METHOD 7 — Favicon Hash (Shodan)
Every framework has a default favicon. Hash it → search Shodan.
# Get favicon hash
```
python3 -c "
import requests, mmh3, codecs
r = requests.get('https://target.com/favicon.ico', verify=False)
favicon = codecs.encode(r.content, 'base64')
hash = mmh3.hash(favicon)
print(hash)
"
```

# Then search Shodan
```
shodan search "http.favicon.hash:-XXXXXXX"
```
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
