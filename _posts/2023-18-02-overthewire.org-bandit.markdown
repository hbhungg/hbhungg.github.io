# overthewire.org Bandit game
These are my solution to the [Bandit game on overthewire.org][https://overthewire.org/wargames/bandit/]

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
## Level 6 → Level 7
## Level 7 → Level 8
## Level 8 → Level 9
## Level 9 → Level 10
## Level 10 → Level 11
## Level 11 → Level 12
## Level 12 → Level 13
## Level 13 → Level 14
## Level 14 → Level 15
## Level 15 → Level 16
## Level 16 → Level 17
## Level 17 → Level 18
## Level 18 → Level 19
## Level 19 → Level 20
