# LEVEL 09

In our home directory, we see an executable with suid/guid and a token file that we can read but which content seems encrypted:
		>$ ls -l
		total 12
		-rwsr-sr-x 1 flag09 level09 7640 Mar  5  2016 level09
		----r--r-- 1 flag09 level09   26 Mar  5  2016 token
		>$ cat token
		f4kmm6p|=�p�n��DB�Du{��

We know the file expects an argument:
		>$ ./level09
		You need to provide only one arg.

Let's figure out what the executable does:
		>$ ./level09 token
		tpmhr
		>$ ./level09 hello
		hfnos
		>$ ./level09 hi
		hj

the executable expects a string as an argument, and encodes it by rotating each character by an incrementing index.
for example: token
t  o  k  e  n
+0 +1 +2 +3 +4
t  p  m  h  r
If this file was used to encrypt the password in the token file, and maybe we can provide our own program to reverse it.
We create a simple C file `decrypt.c` to decrypt this:
		#include <stdio.h>
		#include <unistd.h>

		int main(int ac, char **argv)
		{
			if (ac != 2)
				return (0);
			int i = -1;
			while (argv[1][++i])
				argv[1][i] -= i;
			printf("Flag09 password bach nakhdu l flag : %s\n", argv[1]);
			return (1);
		}
Let's save the VM level09 token to our local machine:
		>$ scp -P 4242 level09@{VM_IP}:token token

		[... VM prompt for level09 password...]
		password: 25749xKZ8L7DkSCwJkT9dyv6f

		>$ ls
		decrypt.c token
		>$ chmod 755 token
Then we compile `decrypt.c` and run with the token to find the decrypted password:
		>$ gcc decrypt.c -o decrypt
		>$ ./decrypt `cat token`
		Flag09 password : f3iji1ju5yuevaus41q1afiuq
Let's use this to log into flag09:
		>$ su flag09
		Password: f3iji1ju5yuevaus41q1afiuq
		Don't forget to launch getflag !
		>$ getflag
		Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z

Vulnerability:
Breakable encryption algorithm => reverse engineering