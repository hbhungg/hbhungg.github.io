---
layout: post
title: OverTheWire Bandit
date:  2023-02-18
published: true
---

These are my solution to the [Bandit game on overthewire.org](https://overthewire.org/wargames/bandit/). They are quite easy and serves as a good introduction to using Linux and its tools. *(I did them because im bored and wanted a memory refresh).*

## Level 0
ssh into the server with the given credentials.
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

<br>

## Level 0 → Level 1
Use `cat` to read file content.
```bash
cat readme
```

<br>

## Level 1 → Level 2
The file name is `-`, which need to be escape (using backslash `\`) so that cat can read it without being confuse as flag.
```bash
cat ./\-
```

<br>

## Level 2 → Level 3
Since the file name have space, those space will also need to be escape.
```bash
cat spaces\ in\ this\ filename
```

<br>

## Level 3 → Level 4
`ls -a` to list all files, including hidden ones.

<br>

## Level 4 → Level 5
The `file` command can be use to see what file type a given file is, and file that are human-readable would return `ASCII text`.
To run `file` on all files, combine it with `find`.
```bash
find inhere/ -exec file {} +
```
The command above will run `find` on the `inhere/` dir and execute `file` on all of files it found.

<br>

## Level 5 → Level 6
The `find` command also have support to find files by size and executability.
```bash
find inhere/ -size 1033c ! -executable -exec file {} + | grep ASCII
```
We use the same trick last level to search for human-readable file, and `grep` it since there might be lots of file.

<br>

## Level 6 → Level 7
`find` to the rescue again, it can also search by user and group.
Since they say that the file is somewhere in the server, just search it in the root dir.
```bash
find / -size 33c -group bandit6 -user bandit7 -exec grep -v denied {} +
```
We need to use `grep` to find files that we can actually read, the `-v` flags is to search for any lines that does not contains the word denied.

*There might be a more correct way to filter out the denied files, since my `grep` weirdly does not filter out, and also return the content inside the correct file as well! Maybe someone knowledgeable about this could help me understand this interaction.*

<br>

## Level 7 → Level 8
```bash
grep millionth data.txt
```

<br>

## Level 8 → Level 9
Linux have a wonderfull command call `uniq`, which find any lines that does not repeat.
We sort the `data.txt` files, so that any same lines will be next to each other (repeated), then remove them.
```bash
sort data.txt | uniq -u
```

<br>

## Level 9 → Level 10
```bash
grep "==" data.txt --text
```

<br>

## Level 10 → Level 11
```bash
cat data.txt | base64 --decode
```

<br>

## Level 11 → Level 12
Rotated by 13 positions is just ROT13, a special Caesar cipher is it own inverse (the encoder is also the decoder).
```python
import codecs
print(codecs.decode("cipher here", "rot13"))
```

<br>

## Level 12 → Level 13
There are much better ways to complete this level (automate it for example), but I was sleepy at that time and just recursively decompressed it by hand and checked the file type using `file` to figured out which decompressor to use.

<br>

## Level 13 → Level 14
This level is trivial since it a prep step for the next level. Just cat the file in `~` for the private ssh key and log in directly using the key.
```bash
chmod 600 sshkey.private
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```
Then `cat /etc/bandit_pass/bandit14` since we log in as bandit14.
## Level 14 → Level 15
Use `nc` to send data to the port.
```bash
nc localhost 30000
```

<br>

## Level 15 → Level 16
Same as the last level, but instead we use `openssl`.
```bash
openssl s_client -connect localhost:30001
```

<br>

## Level 16 → Level 17
Use netstat to search for open port that is listening.
```bash
netstat -plnt
```
Luckily, only 2 ports open in the range 31000-32000. Just simply check for each of them with `openssl`.

<br>

## Level 17 → Level 18
```bash
diff passwords.old passwords.new
```
<br>

## Level 18 → Level 19
Use `scp` to direcly copy files through ssh.
```bash
scp -P 2220 bandit18@bandit.labs.overthewire.org:~/readme .
```

<br>

## Level 19 → Level 20
The given binary file `./bandit20-do` can be use to execute command as another user (similar to `setuid`).
```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

<br>

## Level 20 → Level 21
The way this level is work is that, the given binary `./suconnect` will listen to a specific port, and return the next level password if received the bandit20's password.

We use `nc` to open a port, send the password in with `echo`. The `&` is use to keep this command running in the background, so that we can use the terminal.
```bash
echo "password" | nc -l 4444 &
./suconnect 4444
```
`./suconnect` will read the password we sent into port 4444 from nc, and return the new password.

<br>

## Level 21 → Level 22
Look inside the bandit22's cronjob `cat /etc/cron.d/cronjob_bandit22` return:

```bash
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

It appears that this cronjob will execute the `/usr/bin/cronjob_bandit22.sh` shell script. Inspect the shell script yield:
```bash
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

This shell script would `cat` the password of bandit22 into a temp file. We cannot access the `bandit22` file since we are not bandit22, but we can access the temp file.
```bash
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

<br>

## Level 22 → Level 23
The first few step is similar to the last level.

```bash
$ cat /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

The shell script content:
```bash
$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

What is shellscript does is that it will get the username, do md5 hash on the string `I am user $myname`, and `cat` the password of that current user to the temp file. The output if run is:
```
Copying passwordfile /etc/bandit_pass/bandit22 to /tmp/8169b67bd894ddbb4412f91573b38db3
```
*The `cut` is for cleaning up the leftover characters after the hash. IDK why `md5sum` does this, my machine only have `md5` and it does not produce the leftover characters*

We cannot edit the shell script, so we have to construct the hash ourself.
```bash
$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```

<br>

## Level 23 → Level 24
Same as above, look at `/etc/cron.d/cronjob_bandit24` cronjob.
```bash
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```
Check the `/usr/bin/cronjob_bandit24.sh` script.
```bash
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```
This script look a bit more complicated than the last one. But basically, it will execute any file inside `/var/spool/bandit24/foo` if the owner of that file is bandit23 (us), and delete all files. Which means that any files inside `/var/spool/bandit24/foo` will be executed with bandit24 permission.

Let's check if bandit23 have any permission to that directory.

```bash
$ ls -ld /var/spool/bandit24/foo
drwxrwx-wx 8 root bandit24 4096 Feb 19 12:15 /var/spool/bandit24/foo
```

Notice the last 2 char "wx". It means that we have write and execute permission in it. Let's create a script that will fetch the password like the last level.
```bash
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/password24
```
After waiting for 1 minutes (that's the cronjob schedule), we can cat `/tmp/password24` to get bandit24 password.

<br>

## Level 24 → Level 25
Generate all of the possible combinations of 4-digit code with bandit24 password in form {password} {secret code} into `/tmp/passwords.txt`.
```python
pw = "..."
f = open("/tmp/passwords.txt", "w")
for i in range(0, 10000):
    sc = f"{i:{0}{4}}"
    sent = f"{pw} {sc}\n"
    f.write(sent)
f.close()
```

Then just feed `/tmp/passwords.txt` into the port 30002.
```bash
cat /tmp/passwords.txt | nc localhost 30002
```
*At first, I did not know that you can pass the whole 10000 inputs at the same time, so I created a Python script that talk to the port and receive data for every input. While it was probably correct and could work, I never got to see the answer since the port just kept shutting down (erno 32 broken pipe). So thank you to [this blog](https://mayadevbe.me/posts/overthewire/bandit/level25/).
Also their generator in bash look much nicer than mine.*

<br>

## Level 25 → Level 26
Ok so this level is quite interesting and is definitely require much more "outside the box" thinking. This entry would be different from previous entries, as I am trying to log my thought process.

After login to bandit25, we immediately saw `bandit26.sshkey` (bandit26's private ssh key) in the home dir. Trying to login normally using this ssh key results in a connect and immediate connection close. The level guide said that bandit26 does not use `/bin/bash` but something else, so we should check what shell its using.

```bash
$ cat /etc/passwd | grep bandit26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```
Check for `/usr/bin/showtext` yield:
```bash
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```
So, this script call `more` on `~/text.txt` and immediately exit, that explain why we got immediately kick out of the connection. Going back to the connection output to see if there are any extra text printing out from more, and found this.

```
  _                     _ _ _   ___   __
 | |                   | (_) | |__ \ / /
 | |__   __ _ _ __   __| |_| |_   ) / /_
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/
```

Cute logo, but that does not offer any more clue. At this point, I was quite stuck, I kinda guess that the attack vector would lies in the `more` program here, but not really sure how.

But then I had an epiphany, you can edit the current file `more` is by pressing `v`, and editing file means that you have access to `vim`, or particularly, [its ability to run shell command with `:!`](https://superuser.com/questions/285500/how-to-run-unix-commands-from-within-vim). You can even choose the shell you want to run with `:set shell=/bin/bash`, thereby bypassing the user default bogus shell.

The real kicker of this level is how to stay in `more` pager. Turn out, the solution is to resize your own terminal smaller height wise so that `more` cannot display the whole output. After that, its smooth sailing.
Press `v` to enter Vim, do `:set shell=/bin/bash`, then `:shell`. You now gain `bash` shell.

<br>

## Level 26 → Level 27
This continue directly from the last level after gaining `bash` shell inside Vim. 

There is a binary `bandit27-do`, which is the same as level 19 → level 20. Use it to retrived bandit27's password.

```bash
./bandit27-do cat /etc/bandit_pass/bandit27
```

<br>

## Level 27 → Level 28
```bash
git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
```
The password is in README.md

<br>

## Level 28 → Level 29
```bash
git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
```
The password is in README.md, but in the previous commit. View changes with `git diff HEAD~1`.

<br>

## Level 29 → Level 30 
The password is also in README.md, but in branch `dev`. Do `git checkout dev`.

<br>

## Level 30 → Level 31 
```basn
git tag
git show secret
```

<br>

## Level 31 → Level 32
Follow README.md instruction

<br>

## Level 32 → Level 33
Bandit31's shell is called `UPPERCASE` shell, where every input is uppercase.

```bash
WELCOME TO THE UPPERCASE SHELL
>> ls
sh: 1: LS: not found
>> CD .
sh: 1: CD: not found
```
This lead to problem as it prevent the ability to use all of the commands that have alphabetical in it. To solve this, we have to find any command that are not aphabetical.
Usually, the interactive shell hold what type of shell it is using in `$0`. This is perfect as that particular command does not change when uppercased, and it will open up a new interactive shell. 

```bash
>> $0
$ ls
uppershell
$ cd ../bandit33
$ cat README.txt                                                                                          
```

```
Congratulations on solving the last level of this game!                                                   
                                                                                                          
At this moment, there are no more levels to play in this game. However, we are constantly working                                                                                                                    
on new levels and will most likely expand this game with more levels soon.                                
Keep an eye out for an announcement on our usual communication channels!                                  
In the meantime, you could play some of our other wargames.                                               
                                                     
If you have an idea for an awesome new level, please let us know!
```

