# Secured web Mutillidae II Application 
This web app focuses on identifying and fixing security vulnerabilities in the Mutillidae II deliberately vulnerable web application.
Application's primary purpose is to exercise the web security by intentionally making the website vulnerable. 
It has Web UI layer and database layer.
It has a list of vulnerabilities to be fixed.

## Original vulnerable code repo
https://github.com/webpwnized/mutillidae

## Vulnerabilities chosen to fix
1. CSRF + stored XSS
2. Sensitive data exposure (robots.txt, database-config.inc, HTML/javascript comments)
3. Security Misconfiguration (SSL stripping, SSL/TLS misconfigurations)
4. Injection (Unrestriced file upload)

# Features and Security Objectives
Major functionalities of application include user login, registration, reading/adding blog and file upload to the website.
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
5. robots.txt (Fixed sensitive info disclosure)
6. server.crt (Fixed obsolete SSL/TLS algorithms and ciphers)
7. server.key (Fixed obsolete SSL/TLS algorithms and ciphers)
8. upload-file.php (Fixed unrestricted file upload)

# Setup and Installation Instructions
1. Download XAMPP and Mutillidae original code from GitHub
2. Install XAMPP on machine
3. Copy Mutillidae folder into XAMPP installation folder
4. Start Apache and MySQL service in XAMPP control panel
5. Create Mutillidae databse on phpmyadmin http://localhost/phpmyadmin/index.php
6. Open http://localhost/mutillidae and restart the database to populate the table in Mutillidate database
7. set security level 0 on mutillidae website to make it vulnerable

# Usage Guidelines
1. Navigate to login/register on website to login or register the new user.
2. Toggle security to set the security level of website 0 = insecure, 5= secure (no vulnerabilities)
3. Enforce TLS to enable HTTPS.
4. Application allows creating, deletion, and updating user accounts.
5. It has database and web UI.
6. It has admin level account and user level accounts in database with 500 accounts in total.
7. There is a reset DB button to reset database conneciton if any issues.
8. There is a list of vulnerabilities under different tabs in the left pane.
9. It automatically detects if the MySQL database is available.
10. Application can be restored to default state by clicking setup button.
11. Testers can switch between secure and vulnerable modes.
12. It allows SSL to be enforced to practice HTTPS related vulnerabilities.
13. It can be installed on Linux and Windows using XAMMP, making it convenient for those who do not want to setup web server.

# Security Improvements
1. Implemented **HTML Output Encoding** in add-to-your-blog.php file to fix CSRF + Stored XSS vulnerability in http://localhost/mutillidae/index.php?page=add-to-your-blog.php 
3. Used Gobuster tools in Kali Linux to brute force files and directories of the website, robots.txt file is exposed and it exposes sensitive directory. To fix, browsed robots.txt and delete all contents inside to prevent sensitive data exposure.
4. **database-config.inc** is exposed when browsing http://localhost/mutillidae/includes/database-config.inc. It exposes sensitive databse credential. To fix it, moved the config file from root directory **htdocs\mutillidae\includes** to non-root directory outside of **htdocs**.
5. Used SSLscan tools in Kali Linux against the website, it exposed obsolete **SHA1WithRSAEncryption** in Signature algorithm of SSL certificate and key strength is 1024 which is breakable. To fix it, generated new server cert and key with SHA256 and key size 2048 by using openssl tools in mutillidae folder. Once new key and cert was generated, replace old key and cert file with new ones in **httpd-ssl.conf**.
6.  Used SSLscan tools in Kali Linux against the website, it exposed 64 bits obsolete ciphers used in **httpd-ssl.conf**. To fix it, replace new ciphers suites **SSLCipherSuite HIGH:!AESCCM8:!AESCCM:!MD5:!RC4:!3DES** and **SSLProxyCipherSuite HIGH:!AESCCM8:!AESCCM:!MD5:!RC4:!3DES**
7.  For unrestricted file upload on http://localhost/mutillidae/index.php?page=upload-file.php, it is supposed to allow image files such as jpeg, gif, and png files, but it allows any file type to be uploaded. To fix it, applied secured code to reject file uploading if the wrong file type is uploaded.
8. The website was vulnerable because HSTS (HTTP Strict Transport Security) was disabled. It was known through max-age=0 found in browser developer tool. To fix that, enforced HSTS header in code.
9. For sensitive data exposure vulnerability when browsing on http://localhost/mutillidae/index.php?page=client-side-comments.php page, it exposed database credential when using **view page source function**. To fix it, deleted the code block of credential in **information-disclosure-comment.php **.

# Testing Process
1. SSLscan - to check SSL/TLS ciphers, versions, and key length
2. Gobuster - to discover exposed directories and files on website
3. View page source - to view the website source code
4. Browser developer tool - to check HTTP Strict-Transport-Security header
5. Hack.txt - to check if .txt file is allowed to upload

# Key findings
1. Obsolete cipher suites, signature algorithms, and key were deleted.
2. When browsing to http://localhost/mutillidae/includes/database-config.inc, "The requested URL not found"** is shown to the user.
3. After implementing **HTML Output Encoding**, When browsing http://localhost/mutillidae/index.php?page=add-to-your-blog.php, user input script is no longer executed as a code. It treated as a character and simply add the input in the table.
4. When browsing to localhost/mutillidae/robots.txt, it is no longer showing the sensitve data inside the text file.
5. After restrcting the file upload, only allowed file type is accepted to upload. Hack.txt is no longer accepted.
6. After enabling HSTS, max-age=31536000 is found in browser developer tools when user browse the website on HTTPS.
7. After deleting the credential code block in information-disclosure-comment.php, the exposed credential is no longer exposed when using view page source function on browser.

# Contributions and References
1. https://github.com/webpwnized/mutillidae
2. Conklin K, Drake V, Strittmetter S, Braiterman Z, and Shostack A. (no date) Threat Modeling Process. Available at: https://owasp.org/www-community/Threat_Modeling_Process [Accessed 27 Nov 2025].
3. (no date) Lab 92 – OWASP A6 Security Misconfiguration. Available at: 
https://www.101labs.net/comptia-security/lab-92-owasp-a6-security-misconfiguration  [Accessed 20 Nov 2025].
4. (no date) Lab 89 – OWASP A3 – Sensitive Information Disclosure. Available at: 
https://www.101labs.net/comptia-security/lab-89-owasp-a3-sensitive-information-disclosure [Accessed 20 Nov 2025].
5. (no date) A3:2017-Sensitive Data Exposure. Available at: 
https://owasp.org/www-project-top-ten/2017/A3_2017-Sensitive_Data_Exposure [Accessed 20 Nov 2025].
6. Bhattacharya J. (2025) Mutillidae Uncovered: Exploiting XSS for Beginners (Metasploitable 2 Lab). Available at: https://medium.com/@dave-patten/secure-software-development-lifecycle-ssdlc-8ad9273b395b [Accessed 27 Nov 2025].
7.Patten D. (2024) Secure Software Development Lifecycle (SSDLC). Available at: 
https://medium.com/@unKnOwn37/mutillidae-uncovered-exploiting-xss-for-beginners-metasploitable-2-lab-a5cb66890b89 [Accessed 27 Nov 2025].
8. Jacob A. (2023) Examples of STRIDE threats for payment applications. Available at: 
https://medium.com/@arielhacking/examples-of-stride-threats-for-payment-applications-87a0ad0c3a21 [Accessed 27 Nov 2025].
9. (no date) CWE-219: Storage of File with Sensitive Data Under Web Root. Available at: 
https://cwe.mitre.org/data/definitions/219.html [Accessed 20 Nov 2025].
10. (no date) Runtime Configuration. Available at: 
https://www.php.net/manual/en/session.configuration.php [Accessed 20 Nov 2025].
11. (no date) mysqli_result::$num_rows. Available at: 
https://www.php.net/manual/en/mysqli-result.num-rows.php [Accessed 20 Nov 2025].
12. (no date) mysqli_stmt::get_result. Available at: 
https://www.php.net/manual/en/mysqli-stmt.get-result.php [Accessed 20 Nov 2025].
13. (no date) mysqli_stmt::execute. Available at: 
https://www.php.net/manual/en/mysqli-stmt.execute.php [Accessed 20 Nov 2025].
14. (no date) mysqli_stmt::bind_param. Available at: 
https://www.php.net/manual/en/mysqli-stmt.bind-param.php [Accessed 20 Nov 2025].
15. (no date) mysqli::prepare. Available at: 
https://www.php.net/manual/en/mysqli.prepare.php [Accessed 20 Nov 2025].
16. (no date) SQL Injection: or 1=1 vs ' or 1=1; -- - [duplicate]. Available at: 
https://stackoverflow.com/questions/60939830/sql-injection-or-1-1-vs-or-1-1 [Accessed 20 Nov 2025].
17. (no date) Mutillidae – Sensitive Data Exposure. Available at: 
https://inventyourshit.com/mutillidae-sensitive-data-exposure [Accessed 20 Nov 2025].
18. (no date) HTTP Strict Transport Security Cheat Sheet. Available at: 
https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html [Accessed 20 Nov 2025].
19. Hamid D. (2020) How to Check for HTTPS Request in PHP? Available at: 
https://www.designcise.com/web/tutorial/how-to-check-for-https-request-in-php [Accessed 27 Nov 2025].
20. Everest N. (2020) Setting $_SERVER['HTTPS']='on' prevents access to wp-admin. Available at: 
https://wordpress.stackexchange.com/questions/250240/setting-serverhttps-on-prevents-access-to-wp-admin  [Accessed 27 Nov 2025].
21. (2025) How to check whether the page is called from 'https' or 'http' in PHP? Available at: 
https://www.geeksforgeeks.org/php/how-to-check-whether-the-page-is-called-from-https-or-http-in-php [Accessed 20 Nov 2025].
22. (2025) Business-Logic Layer. Available at: https://www.geeksforgeeks.org/dbms/business-logic-layer [Accessed 20 Nov 2025].
23. Chiaramonti M. (2024) The benefits of a three-layered application architecture. Available at: 
https://vfunction.com/blog/the-benefits-of-a-three-layered-application-architecture [Accessed 27 Nov 2025].


   

   
   

