lets get started with a basic quick rustscan for getting the open ports
```bash
rustscan -a 10.10.223.227
```
found 3 open services:
```bash
PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 60
22/tcp open  ssh     syn-ack ttl 60
80/tcp open  http    syn-ack ttl 60
```
lets see what is on the webpages
so its a basic page which has a team information from the game and linked to another page lets gobuster the site
```bash
gobuster dir -e -u http://10.10.223.227/ -w /usr/share/dirb/wordlists/common.txt
```
so found something in the code and in the results also
so visiting the website we found multiple links and endpoints, its kind of a maze and we have do it on the web and gather relevant information and then further get into the machine.

we got multiple flags hidden in the code encrpted with cipers and rotational cipher etc and decoding it gave us keys and stuff

after getting all the crests we combined all of then and we got the ftp username and password
```bash
FTP user: hunter, FTP pass: you_cant_hide_forever
```
