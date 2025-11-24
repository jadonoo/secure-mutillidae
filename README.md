## Original Base Code
https://github.com/webpwnized/mutillidae

## Vulnerabilities chosen to fix
1. CSRF + stored XSS
2. Sensitive data exposure (robots.txt, database-config.inc, HTML/javascript comments)
3. Security Misconfiguration (SSL stripping, SSL/TLS misconfigurations)
4. Injection (Unrestriced file upload)

# Secured web Mutillidae II Application 
This web app focuses on identifying and fixing security vulnerabilities in the Mutillidae II deliberately vulnerable web application.
Application's primary purpose is to exercise the web security by intentionally making the website vulnerable. 
It has Web UI layer and database layer.
It has a list of vulnerabilities to be fixed.

# Features and Security Objectives
Major functionalities of application include user login, registration, reading blog and file upload to the website.
Security objectives include preventing CSRF and stored XSS, protecting sensitive files form exposure, hardening obsolete SSL/TLS ciphers and algorithms, and preventing unauthorized injection file uploading.

# Project structure
/mutillidae 
- **`ajax`** - Contains files related to AJAX functionality.
- **`classes`** - PHP class files for handling various tasks (e.g., logging, token management, database operations).
- **`data`** - Data files, such as XML data sources.
- **`documentation`** - Documentation files including installation guides and usage instructions.
- **`images`** - All image assets used in the application (e.g., icons, gritter assets).
- **`includes`** - Reusable PHP files (e.g., templates, configuration files).
- **`javascript`** - JavaScript libraries, custom scripts, and initializers for front-end functionality.
- **`labs`** - Files for security testing, offering challenges such as SQL injection, XSS, and file inclusions.
- **`passwords`** - Password-related files (e.g., account data).
- **`styles`** - CSS stylesheets defining the look and feel of the application.
- **`webservices`** - Web services for REST and SOAP APIs.
  - **`includes`** - Reusable components for web services.
  - **`rest`** - REST API files and related documentation.
  - **`soap`** - SOAP service files, including libraries and documentation.
    - **`soap/lib`** - Library files specifically for SOAP service integration.

Important files
1. index.php (Fixed SSL stripping)
2. add-to-your-blog.php (Fixed CSRF + Stored XSS)
3. httpd-ssl.conf (Fixed obsolete SSL/TLS algorithms and ciphers)
4. information-disclosure-comment.php (Fixed HTML/javascript comments)
5. MySQLHandler.php (fixed database-config.inc)
6. robots.txt (Fixed sensitive info disclosure)
7. server.crt (Fixed obsolete SSL/TLS algorithms and ciphers)
8. server.key (Fixed obsolete SSL/TLS algorithms and ciphers)
9. upload-file.php (Fixed unrestricted file upload)

# Setup and Installation Instructions
1. Download XAMPP and download Mutillidate original code from GitHub
2. Install XAMPP
3. Copy Mutillidae folder into XAMPP installation folder
4. Start Apache and MySQL in XAMPP
5. Create Mutillidae databse on phpmyadmin http://localhost/phpmyadmin/index.php
6. Open http://localhost/mutillidae and restart the database to populate the table in Mutillidate database
7. set security level 0 on mutillidae website to fix vulnerabilities

# Usage Guidelines
1. Navigate to login/register on website to login or register the new user.
2. Toggle security to set the security level of website 0 = insecure, 5= secure (no vulnerabilities)
3. Enforce TLS to enable HTTPS
4. Application allows creating, deletion, and updating user accounts.
5. It has database and web UI.
6. It has admin level account and user level accounts in database with 500 accounts in total.


# Security Improvements
1. Implemented **HTML Output Encoding** in add-to-your-blog.php file to fix CSRF + Stored XSS vulnerability in OWASP 2013 >> CSRF >> Add to your blog
2. Used Gobuster tools in Kali Linux to brute force files and directories of the website, robots.txt file is exposed and it exposes sensitive directory. To fix, browsed robots.txt and delete all contents inside to prevent sensitive data exposure.
3. **database-config.inc** is exposed when browsing http://localhost/mutillidae/includes/database-config.inc. It exposes sensitive databse credential. To fix it, moved the config file from root directory **htdocs\mutillidae\includes** to non-root directory outside of **htdocs**.
4. Used SSLscan tools in Kali Linux against the website, it exposed obsolete **SHA1WithRSAEncryption** in Signature algorithm of SSL certificate and key strength is 1024 which is breakable. To fix it, generated new server cert and key with SHA256 and key size 2048 by using openssl tools in mutillidae folder. Once new key and cert was generated, replace old key and cert file with new ones in **httpd-ssl.conf**.
5.  Used SSLscan tools in Kali Linux against the website, it exposed 64 bits obsolete ciphers used in **httpd-ssl.conf**. To fix it, replace new ciphers suites **SSLCipherSuite HIGH:!AESCCM8:!AESCCM:!MD5:!RC4:!3DES** and **SSLProxyCipherSuite HIGH:!AESCCM8:!AESCCM:!MD5:!RC4:!3DES**
6.  For unrestricted file upload on **Othes > Unrestricted file upload > Upload page**, it is supposed to allow image files such as jpeg and png files, but due to vulnerability, it allows any file type where attacker can upload malicious script. To fix it, applied this code to reject file uploading if the wrong file type is uploaded.
**If (!in_array($lFileType, $lAllowedFileTypes)) {
	$lValidationMessage .= " File type {$lFileType} is NOT ALLOWED."; //check if the file type is not in allowed list
	$lFileValid = false; // reject file uploading if wrong file type**
7. The website was vulnerable because HSTS (HTTP Strict Transport Security) was disabled. It was known through max-age=0 found in browser developer tool. To fix that, apply this code.
**if (isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] === 'on') { // Check if the current connection is on HTTPS.
	header('Strict-Transport-Security: max-age=31536000; includeSubDomains', true);** // Forces browsers to always use HTTPS for 1 year including subdomains
    }
8. For sensitive data exposure vulnerability when browsing on **Sensitive data exposure >> Information disclosure >> HTML/Java script comments** page, it exposed user credential when using **view page source function**. To fix it, deleted the code block of credential in **information-disclosure-comment.php **.

# Testing Process
1. SSLscan - to check SSL/TLS ciphers, versions, and key length
2. Gobuster - to discover exposed directories and files on website
3. View page source - to view the website source code
4. Browser developer tool - to check Strict-Transport-Security status
5. Hack.txt - to check if .txt file is allowed to upload

# Key findings
1. Obsolete SSL ciphers removed and no more obsolete signature algorithm and RSA key length
2. When browsing to http://localhost/mutillidae/includes/database-config.inc, "The requested URL not found"** is shown to the user.
3. After implementing **HTML Output Encoding**, When browsing http://localhost/mutillidae/index.php?page=add-to-your-blog.php, user input script is no longer executed as a code. It treated as a character and simply add the input in the table.
4. When browsing to localhost/mutillidae/robots.txt, it is no longer showing the sensitve data inside the text file.
5. After restrcting the file upload, only allowed file type is accepted to upload. Hack.txt is no longer accepted.
6. After enabling HSTS, max-age=31536000 is found in browser developer tools when user browse the website on HTTPS.
7. After deleting the credential code block in information-disclosure-comment.php, the exposed credential is no longer exposed when using view page source function on browser.

# Contributions and References
1. https://github.com/webpwnized/mutillidae

   

   
   

