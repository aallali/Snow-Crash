There is nothing in our home directory.
so we'll have to find a vulnerability in the getflag binary itself using gdb:
      >$ gdb getflag
      (gdb) r
      Starting program: /bin/getflag 
      You should not reverse this
Lets copy the getflag binary outside of the vm:
      >$ scp -P 4242 scp://level00@{VM_IP}/../../../bin/getflag getflag
Then we will inspect it using: https://onlinedisassembler.com/static/home/index.html
We find 15 calls to ft_des function, maybe these could correspond to the 15 flags.
Let's have a look at the corresponding memory addresses in gdb. Create a breakpoint in the main, run it, and jump to memory address:
      >$ gdb getflag
      (gdb) b main
      Breakpoint 1 at 0x804894a
      (gdb) r
      Starting program: /bin/getflag

      Breakpoint 1, 0x0804894a in main ()
      (gdb) ju *0x8048BF3
      Continuing at 0x8048bf3.
      x24ti5gi3x0ol2eh4esiuxias
Great, that's the flag for level00. Searching through the 15 addresses we find:
      (gdb) ju *0x8048DE5
      Continuing at 0x8048de5.
      7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
      >$ su flag14
      Password: 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
      Congratulation. Type getflag to get the key and send it to me the owner of this livecd :)
      >$ getflag
      Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ

Vulnerability:
Weak anti-debugging technique and available gdb => privilege escalation by manipulating registers