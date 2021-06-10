# LEVEL 13

In our home directory, we see an executable with suid/guid bit
```
>$ ls -l
total 8
-rwsr-sr-x 1 flag13 level13 7303 Aug 30  2015 level13
```
let's run it:
```
>$ ./level13
UID 2013 started us but we we expect 4242
```
Look that the program is protected unless our user id is `4242`.
Let's verify our username and user id:
```
>$ whoami
level13
>$ echo $UID
2013
```
Exploit the binary with `gdb` to make the executable believe that we are the user with the UID` 4242`
We decompiled the main, to find the place where it calls the `getuid` function. In the following instruction, we put a break point in order to be able to modify the register `%eax` and assign it the value `0x1092` or `4242` in hexadecimal (see https://www.rapidtables.com/convert/number/decimal-to-hex.html). 
According to Wikipedia :


      eax is a 32-bit general-purpose register with two common uses: 
      to store the return value of a function and as a special register for certain calculations. 
      It is technically a volatile register, since the value isn't preserved. Instead, its value is set to the return value of a function before a function returns. 
      Other than esp, this is probably the most important register to remember for this reason. eax is also used specifically in certain calculations, such as multiplication and division, as a special register. 
      That use will be examined in the instructions section. 


It only remains to run the program in `gdb` with` r`:

```
>$ gdb level13
(gdb) disass main
Dump of assembler code for function main:
      0x0804858c <+0>:	push   %ebp
      0x0804858d <+1>:	mov    %esp,%ebp
      0x0804858f <+3>:	and    $0xfffffff0,%esp
      0x08048592 <+6>:	sub    $0x10,%esp
      0x08048595 <+9>:	call   0x8048380 <getuid@plt>
      0x0804859a <+14>:	cmp    $0x1092,%eax
      0x0804859f <+19>:	je     0x80485cb <main+63>
      0x080485a1 <+21>:	call   0x8048380 <getuid@plt>
      0x080485a6 <+26>:	mov    $0x80486c8,%edx
      0x080485ab <+31>:	movl   $0x1092,0x8(%esp)
      0x080485b3 <+39>:	mov    %eax,0x4(%esp)
      0x080485b7 <+43>:	mov    %edx,(%esp)
      0x080485ba <+46>:	call   0x8048360 <printf@plt>
      0x080485bf <+51>:	movl   $0x1,(%esp)
      0x080485c6 <+58>:	call   0x80483a0 <exit@plt>
      0x080485cb <+63>:	movl   $0x80486ef,(%esp)
      0x080485d2 <+70>:	call   0x8048474 <ft_des>
      0x080485d7 <+75>:	mov    $0x8048709,%edx
      0x080485dc <+80>:	mov    %eax,0x4(%esp)
      0x080485e0 <+84>:	mov    %edx,(%esp)
      0x080485e3 <+87>:	call   0x8048360 <printf@plt>
      0x080485e8 <+92>:	leave
      0x080485e9 <+93>:	ret
End of assembler dump.
```

```
(gdb) break *0x0804859a
Breakpoint 1 at 0x804859a
```
```
(gdb) r
Starting program: /home/user/level13/level13
Breakpoint 1, 0x0804859a in main ()
```
```
(gdb) set $eax = 0x1092
(gdb) s
Single stepping until exit from function main,
which has no line number information.
your token is 2A31L79asukciNyi8uppkEuSx
0xb7e454d3 in __libc_start_main () from /lib/i386-linux-gnu/libc.so.6
(gdb)
```

#### Vulnerability:
* gdb available on the attacked server -> Privilege escalation by register manipulation
* prevent access to unknow users or sources some tools in the protected server to avoid any type of execution manipulation 