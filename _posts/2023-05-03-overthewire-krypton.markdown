---
layout: post
title: OverTheWire Krypton 
date:  2023-05-03
published: false
---

## Level 0 → 1
Base64 decode
```
"S1JZUFRPTklTR1JFQVQ=" -> "KRYPTONISGREAT"
```
<br>

## Level 1 → 2
ROT Cipher
```
"YRIRY GJB CNFFJBEQ EBGGRA" -> "LEVEL TWO PASSWORD ROTTEN"
```
<br>

## Level 2 → 3
Caesar cipher
We have access to the encryption binary, which yields:
```
"ABCDEFGHIJKLMNOPQRSTUVWXYZ" -> "MNOPQRSTUVWXYZABCDEFGHIJKL"
```
We can work reverse on our encrypted data:
```
"OMQEMDUEQMEK" -> "CAESARISEASY"
```
<br>

## Level 3 → 4

<br>

## Level 4 → 5