# overthewire.org Bandit game
These are my solution to the [Bandit game on overthewire.org][https://overthewire.org/wargames/bandit/]
Bandit games are very easy and serve as a good introduction to using Linux. (I did them because im bored and wanted a memory refresh).

## Level 0
ssh into the server with the given credentials.
```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

## Level 0 → Level 1
Use `cat` to read file content.
```
cat readme
```

## Level 1 → Level 2
The file name is "-", which need to be escape (using backslash "\") so that cat can read it without being confuse as flag.
```
cat ./\-
```

## Level 2 → Level 3
Since the file name have space, those space will also need to be escape.
```
cat spaces\ in\ this\ filename
```

## Level 3 → Level 4
Traverse into inhere dir, use `ls -a` to list all files, including hidden ones. Then cat it.

## Level 4 → Level 5
The `file` command can be use to see what file type a given file is, and file that are human-readable would return `ASCII text`.
To run `file` on all files, combine it with `find`.
```
find inhere/ -exec file {} +
```
The command above will run `find` on the `inhere/` dir and execute `file` on all of files it found.

## Level 5 → Level 6
The `find` command also have support to find files by size and executability.
```
find inhere/ -size 1033c ! -executable -exec file {} + | grep ASCII
```
We use the same trick last level to search for human-readable file, and `grep` it since there might be lots of file.

## Level 6 → Level 7
`find` to the rescue again, it can also search by user and group.
Since they say that the file is somewhere in the server, just search it in the root dir.
```
find / -size 33c -group bandit6 -user bandit7 -exec grep -v denied {} +
```
We need to use `grep` to find files that we can actually read, the `-v` flags is to search for any lines that does not contains the word denied.

*There might be a more correct way to filter out the denied files, since my `grep` weirdly does not filter out, and also return the content inside the correct file as well! Maybe someone knowledgeable about this could help me understand this interaction.*

## Level 7 → Level 8
`grep millionth data.txt`.

## Level 8 → Level 9
Linux have a wonderfull command call `uniq`, which find any lines that does not repeat.
We sort the `data.txt` files, so that any same lines will be next to each other (repeated), then remove them.
```
sort data.txt | uniq -u
```

## Level 9 → Level 10
`grep "==" data.txt --text`

## Level 10 → Level 11
`cat data.txt | base64 --decode`

## Level 11 → Level 12
Rotated by 13 positions is just ROT13, a special Caesar cipher is it own inverse (the encoder is also the decoder).
```python
import codecs
print(codecs.decode("cipher here", "rot13"))
```

## Level 12 → Level 13
There are much better ways to complete this level (automate it for example), but I was sleepy at that time and just recursively decompressed it by hand and checked the file type using `file` to figured out which decompressor to use.

## Level 13 → Level 14
This level is trivial since it a prep step for the next level. Just cat the file in `~` for the private ssh key and log in directly using the key.
```
chmod 600 sshkey.private
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```
Then `cat /etc/bandit_pass/bandit14` since we log in as bandit14.
## Level 14 → Level 15
Use `nc` to send data to the port.
```
nc localhost 30000
```

## Level 15 → Level 16
Same as the last level, but instead we use `openssl`.
```
openssl s_client -connect localhost:30001
```

## Level 16 → Level 17
Use netstat to search for open port that is listening.
```
netstat -plnt
```
Luckily, only 2 ports open in the range 31000-32000. Just simply check for each of them with `openssl`.

## Level 17 → Level 18
```
diff passwords.old passwords.new
```
## Level 18 → Level 19
Use `scp` to direcly copy files through ssh.
```
scp -P 2220 bandit18@bandit.labs.overthewire.org:~/readme .
```
## Level 19 → Level 20
The given binary file `./bandit20-do` can be use to execute command as another user (similar to `setuid`).
```
./bandit20-do cat /etc/bandit_pass/bandit20
```

## Level 20 → Level 21
The way this level is work is that, the given binary `./suconnect` will listen to a specific port, and return the next level password if received the bandit20's password.

We use `nc` to open a port, send the password in with `echo`. The `&` is use to keep this command running in the background, so that we can use the terminal.
```
echo "password" | nc -l 4444 &
./suconnect 4444
```
`./suconnect` will read the password we sent into port 4444 from nc, and return the new password.
