# Packet Scanning

## tcpdump

```bash
tcpdump -i eth0
tcpdump -c -i eth0
tcpdump -A -i eth0
tcpdump -w 0001.pcap -i eth0
tcpdump -r 0001.pcap
tcpdump -n -i eth0
tcpdump -i eth0 port 22
tcpdump -i eth0 -src 172.21.10.X
tcpdump -i eth0 -dst 172.21.10.X

# Online service
https://packettotal.com/
```

## Packet strings analyzer

```bash
# https://github.com/lgandx/PCredz
./Pcredz -f file-to-parse.pcap
./Pcredz -d /tmp/pcap-directory-to-parse/
./Pcredz -i eth0 -v
```

