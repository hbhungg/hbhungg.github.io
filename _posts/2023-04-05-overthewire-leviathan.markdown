---
layout: post
title: OverTheWire Leviathan 
date:  2023-04-05
---

Solution to the [Leviathan game on overthewire.org](https://overthewire.org/wargames/leviathan/)

## Level 0
The credential is given.

<br>

## Level 0 → 1
Password is in `.backup/bookmark.html`. 
```
grep leviathan .backup/bookmark.html
```

<br>

## Level 1 → 2 
Use `ltrace` to see which calls the `./check` binary uses.
```
ltrace ./check
```
`./check` use `strcmp` to compare the input with their secret password.
Success input will log into `leviathan2`.

Grab the password for `leviathan2`.
```
cat /etc/leviathan_pass/leviathan2
```
*For some reason, the permission that we gain from `./check` does not have permission to run the next level binary, even though it is still `leviathan2` when doing `whoami`. Does anyone know why?*

<br>

## Level 2 → 3 
Will write the explaination later. This is quite a tricky level.
```
mkdir /tmp/lvl3
ln -s /etc/leviathan_pass/leviathan3 /tmp/lvl3/pass
touch /tmp/lvl3/pass\ hello
./printfile /tmp/lvl3/pass\ hello
```
<br>

## Level 3 → 4 
Same as level 1 → 2. Use `ltrace` to check for calls, find the `strcmp`, take the secret password, gain access to leviathan4, `cat /etc/leviathan_pass/leviathan4`. 

<br>

## Level 4 → 5 
There is a binary `.trash/bin`. Execute it will give back bunchs of binary numbers. Doing `ltrace` would yield this.
```
__libc_start_main(0x80491a6, 1, 0xffffd5e4, 0 <unfinished ...> 
fopen("/etc/leviathan_pass/leviathan5", "r") = 0
+++ exited (status 255) +++
```
So this binary read the leviathan5 password, and the output is possibly password in ASCII binary form.

<br>

## Level 5 → 6 

<br>

## Level 6 → 7 

Brute-force the password.
