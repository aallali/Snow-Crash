# LEVEL 02
```
>$ ls -la
dr-x------ 1 level02 level02  120 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level02 level02  220 Apr  3  2012 .bash_logout
-r-x------ 1 level02 level02 3518 Aug 30  2015 .bashrc
----r--r-- 1 flag02  level02 8302 Aug 30  2015 level02.pcap
-r-x------ 1 level02 level02  675 Apr  3  2012 .profile
```
`.pcap` files (Packet Capture Data) are data files created using the program and they contain the packet data of a network. These files are mainly used in analyzing the network characteristics of a certain data.  

First of all we have to copy level02.pcap on our local machine with the `scp` command so we can work on it. 

On a new terminal window (out of your VM):
`>$ scp -P 4242 level02@{VM_IP}:/home/user/level02/level02.pcap .`



**Cloudshark** is a tool that read the `.pcap ` files
So we go to www.cloudshark.org

`Products` -> `personal SaaS` -> `login` with our personal id 
Then we upload the `.pcap` file on the platform.

Current rights are insufficient to run **level02.pcap** with **Wireshark**, so let's give all users the right to read/write and execute:
`➜ chmod 777 level02.pcap`

Click on the uploded file then follow the transmission : `Analysis Tools` -> `Follow Stream`  
Scroll a little bit and we see :  
`Password: ft_wandr...NDRel.L0L`

Let's try it  
```
>$ su flag02
Password:
su: Authentication failure
```
Seems that it is not the good password.

we go back on cloudshark and follow the transmission again, then we click on `Hex Dump`:  
```
    66 | 74 | 5f | 77 | 61 | 6e | 64 | 72 | 7f | 7f | 7f | 4e | 44 | 52 | 65 | 6c | 7f | 4c | 30 | 4c
   --- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | --
     f |  t |  _ |  w |  a |  n |  d |  r |  . |  . |  . |  N |  D |  R |  e |  l |  . |  L |  O |  L
```

In the ascii-table, the 7f is DEL, so we remove the letters typed before.
```
    66 | 74 | 5f | 77 | 61 | 4e | 44 | 52 | 65 | 4c | 30 | 4c
   --- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | --
     f |  t |  _ |  w |  a |  N |  D |  R |  e |  L |  O |  L
```

Pass : ***ft_waNDReL0L***

Let's try again !  
```
>$ su flag02
Password:
Don't forget to launch getflag !
>$ getflag
Check flag.Here is your token : kooda2puivaav1idi4f57q8iq
```
#### Vulnerability:
* Packets captured over clear text protocol -> password sniffing.
use difficult encryption algos 
* avoid http connections
...
* read this : https://www.logixconsulting.com/2020/08/17/5-tips-to-protect-against-password-sniffing/

#### Documentations :
1. https://www.reviversoft.com/file-extensions/pcap?ncr=1&lang=en
1. https://fileinfo.com/extension/pcap
1. https://osxdaily.com/2015/04/23/sniff-packet-capture-packet-trace-mac-os-x-wireless-diagnostics/
1. https://www.wireshark.org/#download
1. https://fr.wikibooks.org/wiki/Les_ASCII_de_0_%C3%A0_127/La_table_ASCII
1. https://stackoverflow.com/questions/26242156/install-wireshark-on-macos-x-via-brew
1. https://www.wireshark.org/docs/man-pages/tshark.html
1. https://osqa-ask.wireshark.org/questions/15374/dump-raw-packet-data-field-only
1. https://osqa-ask.wireshark.org/questions/45418/specific-ip-address-display-filter-using-tshark