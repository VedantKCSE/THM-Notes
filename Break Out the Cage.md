initial network enumeration
```shell
nmap -sS 10.10.144.2
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-31 22:04 IST
Nmap scan report for 10.10.144.2
Host is up (0.20s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 6.52 seconds
```

lets start with http and analyze it for some clues
nothing special found just got that the persons son "Weston" has built him the site and hosted in the mans house
lets perform a directory bursting to get any other endpoints we can find
```bash
ffuf -u http://10.10.144.2/FUZZ -w /usr/share/wordlists/dirb/big.txt

.htpasswd
.htaccess
contracts
html
images
scripts
auditions
```

we got some interesting endpoints now lets check them 