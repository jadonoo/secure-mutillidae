# Secure Mutillidae II App (Option A)

This CA is based on the deliberately vulnerable Mutillidae II web application.
This CA is to identify existing vulnerabilities and implement security measures as part of CA-2.

## Original Base Code
https://github.com/webpwnized/mutillidae

## Vulnerabilities chosen to fix
1. CSRF + stored XSS
2. Sensitive data exposure (robots.txt, database-config.inc, HTML/javascript comments)
3. Security Misconfiguration (SSL stripping, SSL/TLS misconfigurations)
4. Injection (Unrestriced file upload)

