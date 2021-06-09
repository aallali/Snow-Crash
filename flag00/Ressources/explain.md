
# LEVEL 00
Using the command below we get all files that are created by the user under name `flag00` , that we have access permission to, and `2>/dev/null` is to redirect the `stderr` into the null device for more readability: 
```
>$ find / -user flag00 2> /dev/null
/usr/sbin/john 
/rofs/usr/sbin/john
```
the content of both files is the same :
```
>$ cat /usr/sbin/john 
cdiiddwpgswtgt
```
```
>$ cat /rofs/usr/sbin/john
cdiiddwpgswtgt
```
We tried to connect using it but it doesn't work.
Since this text doesnt match any of the known encoding format, so probably it was encrypted using a caesar cipher encryption method.

We decode the password of user flag00 with : 
- https://tech.pookey.co.uk/non-wp/encoder-decoder.php 

using '**ROT all**' option, we find one more understandable decryption : ***nottoohardhere***

```
ROT 0 :	    cdiiddwpgswtgt
ROT 1 :	    dejjeexqhtxuhu
ROT 2 :	    efkkffyriuyviv
ROT 3 :	    fgllggzsjvzwjw
ROT 4 :	    ghmmhhatkwaxkx
ROT 5 :	    hinniibulxbyly
ROT 6 :	    ijoojjcvmyczmz
ROT 7 :	    jkppkkdwnzdana
ROT 8 :	    klqqllexoaebob
ROT 9 :	    lmrrmmfypbfcpc
ROT 10 :    mnssnngzqcgdqd
ROT 11 :    nottoohardhere  <-----
ROT 12 :    opuuppibseifsf
ROT 13 :    pqvvqqjctfjgtg
ROT 14 :    qrwwrrkdugkhuh
ROT 15 :    rsxxsslevhlivi
ROT 16 :    styyttmfwimjwj
ROT 17 :    tuzzuungxjnkxk
ROT 18 :    uvaavvohykolyl
ROT 19 :    vwbbwwpizlpmzm
ROT 20 :    wxccxxqjamqnan
ROT 21 :    xyddyyrkbnrobo
ROT 22 :    yzeezzslcospcp
ROT 23 :    zaffaatmdptqdq
ROT 24 :    abggbbunequrer
ROT 25 :    bchhccvofrvsfs
```

We connect as user flag00 using the password with the command :
```
>$ su flag00
```
After we connected to user flag00, we run \``getflag`\` command (check subject) and we get the flag for the next level.

```
level00@SnowCrash:~$ su flag00
Password:
    Don't forget to launch getflag !
flag00@SnowCrash:~$ getflag
    Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias
```

#### TOOLS :
- Cipher decyption : https://tech.pookey.co.uk/non-wp/encoder-decoder.php
#### Vulnerability:
- Weak password encryption -> use complicated encryption algos

#### Documentations :
- 2> /dev/null :: https://askubuntu.com/questions/350208/what-does-2-dev-null-mean
- CIPHER encryption :: https://en.wikipedia.org/wiki/Cipher  
