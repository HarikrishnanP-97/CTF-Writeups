TheNotebook | HTB | 10.10.10.230

Nmap scan
---------
22/tcp    open     ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp    open     http    nginx 1.14.0 (Ubuntu)

* visit the website and we see nothing interesting , just a usual website to add notes 
* register and login and we see an option to add our own notes 
* intercept the request with burp and we can see that we have a JWT token
* grab the JWT token and decode it in jwt.io
* we see a few interesting stuffs 
* the kid (key identity) value is exposed to us and we can see the path of the secret
* making changes to the tokens 
	# change the admin cap to 1
	# change the ip in the header to our ip
* genarate a ssh keys for authentications since the site uses RS256 authentication it requires both private key and public key
* setup a python server in the directory of the public key we generated , this is for the authentication of the private key when checked by the server
* generate the edited jwt token from jwt.one with the necessary changes
	# edit the header for the ip
	# edit the payload for the admin cap change the admin cap to 1
* paste the jwt token to the cookies values of the page we are logged in and refresh the page and we can see that we are in the admin panel 
* we can now read the notes written by others and access the upload page 
* upload a php reverse shell ans setup a nc listener
* run the reverse shell by clicking on the view button in the site   
* and now we have a shell
* from the notes we had access to before , i have noticed a note saying regular backups are planned so searched for backups
* we found an interesting file in /var/backups
* cd to /var/backups and we can see a compressed file called home.tar.gz
* transfer the file to the attacker machine with a python server and unzip the file and we get a ssh private key 
* i have seen a possible username noah before in the notes i had access to
* ssh as noah with ssh private key and we are in
* grab the user.txt

-------------------------------------------
user.txt : a049dc9c25d74521567e2fc7ce82b0a4
-------------------------------------------

* sudo -l and we can see that we can run a docker command as root 
* the version of docker installed on the machine is 18.02 which is vulnerable to the docker container breakout exploit
* sudo docker exec -it webapp-dev01 bash
* now we are the root of the docker but still cant access the root flag 
* googling around leads to a github page with a golang exploit 
* download the main.go script and edit it to include a bash reverse shell
* compile the script with the command : go build main.go
* transfer the compiled file to the victim machine with a python server and wget to download the file into the victim machine
* setup an nc listener
* chmod the main to executable
* ssh into the noah with another terminal and setup the breakout code ready to roll
* run the exploit and run the breakout docker command 
* and now we are root 
* grab the root flag

-------------------------------------------
root.txt : 454f70f3404aa83f0b8e8db4c2e04381
------------------------------------------- 
