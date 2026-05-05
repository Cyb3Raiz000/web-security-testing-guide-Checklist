# OWASP WSTG Penetration Test Checklist

> Web Security Testing Guide — 12 categories, ~100 test cases.

---

## OTG-INFO — Information Gathering

- [ ] WSTG-INFO-01 Search engine recon (Google dorks, Shodan)
- [ ] WSTG-INFO-02 Web server fingerprint (headers, banners)
- [ ] WSTG-INFO-03 Webserver metafiles (robots.txt, sitemap.xml)
- [ ] WSTG-INFO-04 Enumerate app on webserver
- [ ] WSTG-INFO-05 Review webpage content for leaks
- [ ] WSTG-INFO-06 Identify app entry points
- [ ] WSTG-INFO-07 Map execution paths
- [ ] WSTG-INFO-08 Fingerprint web framework
- [ ] WSTG-INFO-09 Fingerprint web app
- [ ] WSTG-INFO-10 Map app architecture

---

## OTG-CONF — Configuration & Deployment

- [ ] WSTG-CONF-01 Network/infra config test
- [ ] WSTG-CONF-02 App platform config test
- [ ] WSTG-CONF-03 File extension handling
- [ ] WSTG-CONF-04 Backup/unreferenced files
- [ ] WSTG-CONF-05 HTTP methods allowed (PUT/DELETE?)
- [ ] WSTG-CONF-06 HTTP Strict Transport Security
- [ ] WSTG-CONF-07 Cross-domain policy
- [ ] WSTG-CONF-08 RIA cross-domain policy
- [ ] WSTG-CONF-09 File permission check
- [ ] WSTG-CONF-10 Subdomain takeover
- [ ] WSTG-CONF-11 Cloud storage misconfiguration

---

## OTG-IDNT — Identity Management

- [ ] WSTG-IDNT-01 Role definition test
- [ ] WSTG-IDNT-02 User registration process
- [ ] WSTG-IDNT-03 Account provisioning process
- [ ] WSTG-IDNT-04 Account enumeration (login/forgot pw)
- [ ] WSTG-IDNT-05 Weak username policy

---

## OTG-ATHN — Authentication

- [ ] WSTG-ATHN-01 Creds transported over encrypted channel?
- [ ] WSTG-ATHN-02 Default credentials test
- [ ] WSTG-ATHN-03 Account lockout mechanism
- [ ] WSTG-ATHN-04 Bypass auth schema
- [ ] WSTG-ATHN-05 Vulnerable remember password
- [ ] WSTG-ATHN-06 Browser cache weakness
- [ ] WSTG-ATHN-07 Weak password policy
- [ ] WSTG-ATHN-08 Weak security questions
- [ ] WSTG-ATHN-09 Weak password reset
- [ ] WSTG-ATHN-10 Weak alternative auth channel
- [ ] WSTG-ATHN-11 MFA bypass

---

## OTG-AUTHZ — Authorization

- [ ] WSTG-AUTHZ-01 Directory traversal / file include
- [ ] WSTG-AUTHZ-02 Bypass authorization schema
- [ ] WSTG-AUTHZ-03 Privilege escalation
- [ ] WSTG-AUTHZ-04 IDOR (Insecure Direct Object Reference)

---

## OTG-SESS — Session Management

- [ ] WSTG-SESS-01 Session management schema test
- [ ] WSTG-SESS-02 Cookie attributes (HttpOnly, Secure, SameSite)
- [ ] WSTG-SESS-03 Session fixation
- [ ] WSTG-SESS-04 Exposed session variables
- [ ] WSTG-SESS-05 CSRF
- [ ] WSTG-SESS-06 Logout function test
- [ ] WSTG-SESS-07 Session timeout
- [ ] WSTG-SESS-08 Session puzzling
- [ ] WSTG-SESS-09 Session hijacking

---

## OTG-INPV — Input Validation

- [ ] WSTG-INPV-01 Reflected XSS
- [ ] WSTG-INPV-02 Stored XSS
- [ ] WSTG-INPV-03 HTTP verb tampering
- [ ] WSTG-INPV-04 HTTP parameter pollution
- [ ] WSTG-INPV-05 SQL injection
- [ ] WSTG-INPV-06 LDAP injection
- [ ] WSTG-INPV-07 XML injection
- [ ] WSTG-INPV-08 SSI injection
- [ ] WSTG-INPV-09 XPath injection
- [ ] WSTG-INPV-10 IMAP/SMTP injection
- [ ] WSTG-INPV-11 Code injection
- [ ] WSTG-INPV-12 Command injection (OS)
- [ ] WSTG-INPV-13 Buffer overflow
- [ ] WSTG-INPV-14 Format string injection
- [ ] WSTG-INPV-15 Incubated vulnerability
- [ ] WSTG-INPV-16 HTTP splitting/smuggling
- [ ] WSTG-INPV-17 Open redirect
- [ ] WSTG-INPV-18 SSTI (Server-Side Template Injection)
- [ ] WSTG-INPV-19 SSRF

---

## OTG-ERRH — Error Handling

- [ ] WSTG-ERRH-01 Improper error handling (stack traces leaked?)
- [ ] WSTG-ERRH-02 Stack traces reveal tech stack?

---

## OTG-CRYP — Cryptography

- [ ] WSTG-CRYP-01 Weak TLS (SSLv3, TLS 1.0, RC4, NULL ciphers)
- [ ] WSTG-CRYP-02 Padding oracle
- [ ] WSTG-CRYP-03 Sensitive data sent unencrypted
- [ ] WSTG-CRYP-04 Weak encryption (ECB mode, MD5, SHA1)

---

## OTG-BUSL — Business Logic

- [ ] WSTG-BUSL-01 Business logic data validation
- [ ] WSTG-BUSL-02 Ability to forge requests
- [ ] WSTG-BUSL-03 Integrity checks
- [ ] WSTG-BUSL-04 Process timing abuse
- [ ] WSTG-BUSL-05 Function use limits (rate limit bypass)
- [ ] WSTG-BUSL-06 Workflow circumvention
- [ ] WSTG-BUSL-07 App misuse defense
- [ ] WSTG-BUSL-08 Unexpected file upload types

---

## OTG-CLNT — Client-Side

- [ ] WSTG-CLNT-01 DOM-based XSS
- [ ] WSTG-CLNT-02 JavaScript execution
- [ ] WSTG-CLNT-03 HTML injection
- [ ] WSTG-CLNT-04 Client-side URL redirect
- [ ] WSTG-CLNT-05 CSS injection
- [ ] WSTG-CLNT-06 Client-side resource manipulation
- [ ] WSTG-CLNT-07 CORS misconfiguration
- [ ] WSTG-CLNT-08 Clickjacking (X-Frame-Options missing?)
- [ ] WSTG-CLNT-09 WebSockets test
- [ ] WSTG-CLNT-10 Web messaging (postMessage abuse)
- [ ] WSTG-CLNT-11 Browser storage (localStorage secrets)
- [ ] WSTG-CLNT-12 Cross-site script inclusion

---

## OTG-APIT — API Testing

- [ ] WSTG-APIT-01 GraphQL security (introspection enabled?)
- [ ] WSTG-APIT-02 REST API auth + rate limit
- [ ] WSTG-APIT-03 Mass assignment (extra fields accepted?)

---

## Pentest Workflow

```
Recon        →  INFO + CONF
Auth Attack  →  ATHN + AUTHZ + SESS
Fuzzing      →  INPV
Logic Abuse  →  BUSL
Client-Side  →  CLNT + APIT
Report       →  map each finding → WSTG ID + CVSS score
```

---

## References

- [OWASP WSTG](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP ZAP](https://www.zaproxy.org/)
