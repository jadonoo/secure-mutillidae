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
6. Open http://localhost/mutillidae and reset the database to populate the table in Mutillidate database
7. set security level 0 on mutillidae website to make it vulnerable
8. Installation setup can be found on installation.php as well

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
7.  For unrestricted file upload on http://localhost/mutillidae/index.php?page=upload-file.php, it is supposed to allow image files only, but it allows any file type to be uploaded. To fix it, applied secured code to reject file uploading if the wrong file type is uploaded.
8. The website was vulnerable because HSTS (HTTP Strict Transport Security) was disabled. It was known through max-age=0 found in browser developer tool. To fix that, enforced HSTS header in code.
9. For sensitive data exposure vulnerability when browsing on http://localhost/mutillidae/index.php?page=client-side-comments.php page, it exposed database credential when using **view page source function**. To fix it, deleted the code block of credential in **information-disclosure-comment.php **.

# Testing Process
Manual functional and Static Application Security Testing (SAST) on three major security features was performed after applying security controls.
1. Reflected XSS mitigation
<script>alert(9)</script> JavaScript was submitted in the comment field of Add to your blog page. The script was simply added and displayed in the table, not executed as script as before. For functional test, submitted normal text in the comment. It was added and displayed on the table. Refer to figure 3.3.1 in appendix.

2. Restricting uploading disallowed file type
Uploaded disallowed file type hack.txt on http://localhost/mutillidae/index.php?page=upload-file.php page. File type was not allowed response was returned to the user. For functional test, uploaded the allowed image file, file was successfully uploaded to the xampp\tmp directory. Refer to figure 3.3.6 in appendix.

3. SSL/TLS hardening
Used SSLscan to scan the website’s TLS setting, SHA2RSAEncryption with 2048 bits key was displayed. Previously supported 64 bits CCM8 cipher suites were not displayed. New SSL certificate was in use and displayed on the website by viewing certificate details. Refer to figure 3.3.4 in appendix.

4. Enforced HTTP strict-transport-security
After enabling HSTS, closed the browser and re-launch the application on HTTPS. Entered into browser develope tools and discovered that Strict-transport-security was set to max-age=31536000 includeSubDomains. It was confirmed that user browser will always connect to the application on HTTPS for next 1 year. Refer to figure 3.3.5 in appendix.

5. Securing exposed directories and database configuration
After deleting the directory information on robots.txt which was provided only for internet crawlers, re-visited the http://localhost/mutillidae/robots.txt to check if the directory information are still visible. After fix, it was confirmed that directory information were no longer visible. Refer to figure 3.3.3 in appendix.
For exposed database configuration, re-visited the page http://localhost/mutillidae/includes/database-config.inc to see if the configurations are still exposed. After fix, user received 404 error, confirming that database configurations was secured. Refer to figure 3.3.2 in appendix.
 
6. Securing exposed database credential
After deleting the database credential on information-disclosure-comment.php, re-visited the page http://localhost/mutillidae/index.php?page=client-side-comments.php and observed that previously exposed database credential was no longer visible in the page source code. Refer to figure 3.3.7 in appendix.

# Key findings
1. Obsolete cipher suites, signature algorithms, and key were deleted.
2. When browsing to http://localhost/mutillidae/includes/database-config.inc, "The requested URL not found"** is shown to the user.
3. After implementing **HTML Output Encoding**, When browsing http://localhost/mutillidae/index.php?page=add-to-your-blog.php, user input script is no longer executed as a code. It treated as a character and simply add the input in the table.
4. When browsing to localhost/mutillidae/robots.txt, it is no longer showing the sensitve data inside the text file.
5. After restrcting the file upload, only allowed image file type is accepted to upload. Hack.txt is no longer accepted.
6. After enabling HSTS, max-age=31536000 is found in browser developer tools when user browse the website on HTTPS.
7. After deleting the credential code block in information-disclosure-comment.php, the exposed credential is no longer exposed when using view page source function on browser.

# Contributions and References
Conklin K, Drake V, Strittmetter S, Braiterman Z, and Shostack A. (no date) Threat Modeling Process. Available at: https://owasp.org/www-community/Threat_Modeling_Process [Accessed 27 Nov 2025].

(no date) Lab 92 – OWASP A6 Security Misconfiguration. Available at: 
https://www.101labs.net/comptia-security/lab-92-owasp-a6-security-misconfiguration  [Accessed 20 Nov 2025].

(no date) Lab 89 – OWASP A3 – Sensitive Information Disclosure. Available at: 
https://www.101labs.net/comptia-security/lab-89-owasp-a3-sensitive-information-disclosure [Accessed 20 Nov 2025].

(no date) A3:2017-Sensitive Data Exposure. Available at: 
https://owasp.org/www-project-top-ten/2017/A3_2017-Sensitive_Data_Exposure [Accessed 20 Nov 2025].

Dizdar A. (2020) The Ultimate Beginners Guide to XSS Vulnerability. Available at: https://brightsec.com/blog/cross-site-scripting-xss [Accessed 20 Nov 2025].

louiepaguilar. (2017) <script>alert(1);</script>. Available at: https://gist.github.com/beatak/1556871 [Accessed 20 Nov 2025].

(no date) Cross-site scripting contexts. Available at: https://portswigger.net/web-security/cross-site-scripting/contexts [Accessed 20 Nov 2025].

Bhattacharya J. (2025) Mutillidae Uncovered: Exploiting XSS for Beginners (Metasploitable 2 Lab). Available at: https://medium.com/@dave-patten/secure-software-development-lifecycle-ssdlc-8ad9273b395b [Accessed 27 Nov 2025].

Patten D. (2024) Secure Software Development Lifecycle (SSDLC). Available at: 
https://medium.com/@unKnOwn37/mutillidae-uncovered-exploiting-xss-for-beginners-metasploitable-2-lab-a5cb66890b89 [Accessed 27 Nov 2025].

Jacob A. (2023) Examples of STRIDE threats for payment applications. Available at: 
https://medium.com/@arielhacking/examples-of-stride-threats-for-payment-applications-87a0ad0c3a21 [Accessed 27 Nov 2025].

(no date) CWE-219: Storage of File with Sensitive Data Under Web Root. Available at: 
https://cwe.mitre.org/data/definitions/219.html [Accessed 20 Nov 2025].

(no date) Runtime Configuration. Available at: 
https://www.php.net/manual/en/session.configuration.php [Accessed 20 Nov 2025].

(no date) mysqli_result::$num_rows. Available at: 
https://www.php.net/manual/en/mysqli-result.num-rows.php [Accessed 20 Nov 2025].

(no date) mysqli_stmt::get_result. Available at: 
https://www.php.net/manual/en/mysqli-stmt.get-result.php [Accessed 20 Nov 2025].

(no date) mysqli_stmt::execute. Available at: 
https://www.php.net/manual/en/mysqli-stmt.execute.php [Accessed 20 Nov 2025].

(no date) mysqli_stmt::bind_param. Available at: 
https://www.php.net/manual/en/mysqli-stmt.bind-param.php [Accessed 20 Nov 2025].

(no date) mysqli::prepare. Available at: 
https://www.php.net/manual/en/mysqli.prepare.php [Accessed 20 Nov 2025].

(no date) htmlspecialchars. Available at: 
https://www.php.net/manual/en/function.htmlspecialchars.php [Accessed 20 Nov 2025].

(no date) Predefined Constants. Available at: 
https://www.php.net/manual/en/string.constants.php#constant.html-specialchars [Accessed 20 Nov 2025].

(no date) PHP 8.1: HTML entity en/decode functions process single quotes and substitute by default. Available at: 
https://php.watch/versions/8.1/html-entity-default-value-changes  [Accessed 20 Nov 2025].

(no date) When Should You Use Htmlspecialchars() Instead Of Htmlentities ()?. Available at: 
https://www.uptimia.com/questions/when-should-you-use-htmlspecialchars-instead-of-htmlentities  [Accessed 20 Nov 2025].

Zahid S. (2013) utf-8 in htmlspecialchars() function. Available at: 
https://stackoverflow.com/questions/16951306/utf-8-in-htmlspecialchars-function [Accessed 27 Nov 2025].

Emmet (2022) Using the htmlspecialchars() Function in PHP. Available at: 
https://pimylifeup.com/php-htmlspecialchars [Accessed 27 Nov 2025].

Hogue E. (2008) htmlentities() vs. htmlspecialchars(). Available at: 
https://stackoverflow.com/questions/46483/htmlentities-vs-htmlspecialchars [Accessed 27 Nov 2025].

Anon. (2011) let htmlspecialchars use UTF-8 as default charset?. Available at: 
https://stackoverflow.com/questions/6181299/let-htmlspecialchars-use-utf-8-as-default-charset [Accessed 27 Nov 2025].

(no date) Should I use ENT_QUOTES with htmlspecialchars or not. Available at: 
https://stackoverflow.com/questions/21171941/should-i-use-ent-quotes-with-htmlspecialchars-or-not [Accessed 20 Nov 2025].

Gupta P. (2024) What is htmlspecialchars() Function in PHP? Available at: 
https://www.geeksforgeeks.org/php/what-is-htmlspecialchars-function-in-php [Accessed 20 Nov 2025].

(no date) mysqli_result::fetch_object. Available at: 
https://www.php.net/manual/en/mysqli-result.fetch-object.php [Accessed 20 Nov 2025].

(no date) How to fetch and return query results?. Available at: 
https://stackoverflow.com/questions/62945530/how-to-fetch-and-return-query-results [Accessed 20 Nov 2025].

(no date) How does while ($row = $result->fetch_object()) work? [closed]. Available at: 
https://stackoverflow.com/questions/12031119/how-does-while-row-result-fetch-object-work [Accessed 20 Nov 2025].

(no date) XSS Injections with htmlspecialchars($string, ENT_QUOTES, 'UTF-8');. Available at: 
https://forums.phpfreaks.com/topic/293823-xss-injections-with-htmlspecialcharsstring-ent_quotes-utf-8 [Accessed 20 Nov 2025].

(no date) PHP htmlspecialchars() Function. Available at: 
https://www.w3schools.com/php/func_string_htmlspecialchars.asp [Accessed 20 Nov 2025].

(no date) htmlspecialchars & ENT_QUOTES not working?. Available at: 
https://stackoverflow.com/questions/4722727/htmlspecialchars-ent-quotes-not-working [Accessed 20 Nov 2025].

(no date) Cross Site Scripting Prevention Cheat Sheet. Available at: 
https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html [Accessed 20 Nov 2025].

(no date) SQL Injection: or 1=1 vs ' or 1=1; -- - [duplicate]. Available at: 
https://stackoverflow.com/questions/60939830/sql-injection-or-1-1-vs-or-1-1 [Accessed 20 Nov 2025].

(no date) Mutillidae – Sensitive Data Exposure. Available at: 
https://inventyourshit.com/mutillidae-sensitive-data-exposure [Accessed 20 Nov 2025].

(no date) HTTP Strict Transport Security Cheat Sheet. Available at: 
https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html [Accessed 20 Nov 2025].

Hamid D. (2020) How to Check for HTTPS Request in PHP? Available at: 
https://www.designcise.com/web/tutorial/how-to-check-for-https-request-in-php [Accessed 27 Nov 2025].

Everest N. (2020) Setting $_SERVER['HTTPS']='on' prevents access to wp-admin. Available at: 
https://wordpress.stackexchange.com/questions/250240/setting-serverhttps-on-prevents-access-to-wp-admin  [Accessed 27 Nov 2025].

(2025) How to check whether the page is called from 'https' or 'http' in PHP? Available at: 
https://www.geeksforgeeks.org/php/how-to-check-whether-the-page-is-called-from-https-or-http-in-php [Accessed 20 Nov 2025].

(2025) Business-Logic Layer. Available at: https://www.geeksforgeeks.org/dbms/business-logic-layer [Accessed 20 Nov 2025].

(no date) PHP for kids. Available at: 
https://www.phpforkids.com/php/php-mysql-database-handling-queries.php [Accessed 20 Nov 2025].

Chiaramonti M. (2024) The benefits of a three-layered application architecture. Available at: 
https://vfunction.com/blog/the-benefits-of-a-three-layered-application-architecture [Accessed 27 Nov 2025].

So-Sc. (no date) OWASP-mutillidae-2. Available at: 
https://github.com/so-sc/OWASP-mutillidae-2/blob/master/installation.php [Accessed 20 Nov 2025].

(no date) Installing Mutillidae on Windows. Available at: 
https://subscription.packtpub.com/book/security/9781788624039/1/ch01lvl1sec03/installing-mutillidae-on-windows  [Accessed 20 Nov 2025].

Khawaja. (2018) Installing Mutillidae on Windows. Available at: 
https://www.packtpub.com/en-us/product/practical-web-penetration-testing-9781788624039/chapter/building-a-vulnerable-web-application-lab-1/section/installing-mutillidae-on-windows-ch01lvl1sec03 [Accessed 20 Nov 2025].

(2017) Facing Problem while installing Mutillidae. Available at: 
https://community.apachefriends.org/viewtopic.php?p=258119&sid=d26a6f98921746d89c5d5140f0f0a966   [Accessed 20 Nov 2025].

Suru M. (2023) Step-by-Step Guide to Installing Mutillidae, a Pen-Testing Demo. Available at: 
https://manitechy.medium.com/tep-by-step-guide-to-installing-mutillidae-a-pen-testing-demo-78b7d5292073  [Accessed 20 Nov 2025].

Kartik. (2016) Mutillidae : installation on WAMP or XAMPP. Available at: 
https://r00tsecurity.wordpress.com/2016/04/23/test-r00t_security-post [Accessed 20 Nov 2025].

(no date) How to find out if you're using HTTPS without $_SERVER['HTTPS']. Available at: 
https://stackoverflow.com/questions/1175096/how-to-find-out-if-youre-using-https-without-serverhttps 
[Accessed 20 Nov 2025].

(no date) Statement that checks whether a URL contains a particular path? [duplicate]. Available at: 
https://stackoverflow.com/questions/52791916/statement-that-checks-whether-a-url-contains-a-particular-path/52792039#52792039  
[Accessed 20 Nov 2025].

(no date) Check whether form submitted URL has HTTPS or not. Available at: 
https://stackoverflow.com/questions/44035902/check-whether-form-submitted-url-has-https-or-not 
[Accessed 20 Nov 2025].

(2021) Fix for some variables($_SERVER['HTTPS'], $_SERVER['SERVER_PORT'],
 $_SERVER['REQUEST_URI']) for Wordpress based site, which prevents 'Too many redirects' error. Available at: https://github.com/nginx/unit/issues/545 [Accessed 20 Nov 2025].

(no date) How can I force users to access my page over HTTPS instead of HTTP?. Available at: 
https://stackoverflow.com/questions/85816/how-can-i-force-users-to-access-my-page-over-https-instead-of-http  
[Accessed 20 Nov 2025].

(no date) OpenSSL Documentation. Available at: https://docs.openssl.org/1.1.1/man1/ciphers/ 
[Accessed 20 Nov 2025].

(no date) Forcing http/https: How to detect https and which status header to send when redirecting?.Available at: 
https://stackoverflow.com/questions/8902419/forcing-http-https-how-to-detect-https-and-which-status-header-to-send-when-red [Accessed 20 Nov 2025].

(no date) if (isset($this->request->server['HTTPS']) Not working. Available at: 
https://forum.opencart.com/viewtopic.php?t=46998 [Accessed 20 Nov 2025].


