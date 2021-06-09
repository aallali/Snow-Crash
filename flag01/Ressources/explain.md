
#LEVEL 01

Traditional Unix systems keeps the user account's information(including encrypted passwords) in a text file called `/etc/passwd` ([sources](http://www.linux-france.org/article/sys/lame/html/x829.html)).

According to [this discussion]([sources](https://security.stackexchange.com/questions/92766/what-can-hackers-do-with-ability-to-read-etc-passwd)) : most systems did this in the past, before using the more secure `/etc/shadow`.
Back then, it took some work to crack hashed passwords. Nowadays, there are tools to try millions of combinations!

`/etc/passwd` stores user account details. Let's look for the password for user `flag01`. 
Note: [the second part of the row of flag01 (which is the current user)](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/) is the password.

```
>$ cat /etc/passwd
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
-> 42hDRfypTqqnw *Let's try this password*  
>$ su flag01
Password:
su: Authentication failure
```

Again an encoded password, this time JohnTheRipper will help us.
why JohnTheRipper? An index is given to us in the `level00`: the file which contains the index is called
`john`. In addition, youtube can tell us more about [John the Ripper](http://www.linuxcertif.com/man/8/john/).

**John is an open source tool that basically decrypts hashed passwords by autodetecting the password's hash type.**

we downloaded it from its official website:
https://www.openwall.com/john


Let's copy the full line containing flag01 "**...42hDRfypTqqnw...**" to a file on our local machine:

`➜ echo "flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash" > passwd`

We install john the ripper there which will be used to break the hash of the password;


```
➜ curl -L --output john.zip https://download.openwall.net/pub/projects/john/contrib/macosx/john-1.8.0.9-jumbo-macosx_sse4.zip
➜ unzip -q john.zip ; mv john-1.8.0.9-jumbo-macosx_sse4 john
```

Then, run john on that file:
```
➜ john --show passwd
flag01:abcdefg:3001:3001::/home/flag/flag01:/bin/bash
1 password hash cracked, 0 left
```
Let's try to log in to user flag01 :
````
>$ su flag01
Password: abcdefg
Run getflag!
>$ getflag
Check flag.Here is your token : f2av5il02puano7naaf6adaaf
````
#### Vulnerability:
* Password stored in publicly readable file, weak encryption -> Crack password with brute-force.
* read more : https://phoenixnap.com/kb/prevent-brute-force-attacks

#### Documentations :
1. http://www.linux-france.org/article/sys/lame/html/x829.html
1. https://security.stackexchange.com/questions/92766/what-can-hackers-do-with-ability-to-read-etc-passwd
1. https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/
1. http://www.linuxcertif.com/man/8/john/
1. https://null-byte.wonderhowto.com/how-to/crack-shadow-hashes-after-getting-root-linux-system-0186386/
1. https://null-byte.wonderhowto.com/how-to/hack-like-pro-crack-user-passwords-linux-system-0147164/
1. https://www.openwall.com/john/
1. https://www.blackmoreops.com/2015/11/10/cracking-password-in-kali-linux-using-john-the-ripper/
1. https://linuxhint.com/john_ripper_ubuntu/
1. http://www.linux-france.org/article/sys/lame/html/x829.html