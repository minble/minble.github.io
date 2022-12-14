---
title: "๐ป(Linux) Symbolic links and hard links"
author: minble
date: 2022-10-05 00:00:00 +0800
categories: [Linux]
tags: [symbolic link, hard link]
---

## ์ฌ๋ณผ๋ฆญ ๋งํฌ (Symbolic link)
์ฌ๋ณผ๋ฆญ ๋งํฌ(๋๋ ์ํํธ ๋งํฌ, Soft link)๋ ์๋์ฐ์ ๋ฐ๋ก๊ฐ๊ธฐ์ ๋น์ทํ ๊ฐ๋์ผ๋ก ์๋ณธ ํ์ผ๊ณผ ์ฐ๊ฒฐ๋ ํ์ผ์๋๋ค. ์ฌ๋ณผ๋ฆญ ๋งํฌ๋ ์๋์ ๊ฐ์ ์ํฉ์์ ์ ์ฉํ๊ฒ ์ฌ์ฉ๋ฉ๋๋ค. [^1]
- ํ์ผ์ ๋ถํ์ํ ๋ฐ๋ณต์  ๋ณต์ฌ๋ฅผ ๋ฐฉ์งํ์ฌ ํ์ผ ์์คํ์ ํ์ฉํ๋ ๊ฒฝ์ฐ
- ์ฌ๋ฌ ๋๋ ํ ๋ฆฌ์์ ๋์ผํ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ์๊ตฌํ๋ ๊ฒฝ์ฐ
- ๊ฐ์ ํ์ผ์ ์ฌ๋ฌ ์ฌ๋์ด ๊ณตํต์ผ๋ก ๊ด๋ฆฌํ๋ฉด์ ์ฌ์ฉํ๋ ๊ฒฝ์ฐ
- ๋น์ทํ ์ญํ ์ ๋ด๋นํ๋ ์ฌ๋ฌ๊ฐ์ง ํ์ผ๋ค ์ค ํ๋๋ฅผ ์ ํํ์ฌ ๋ํ์ ์ผ๋ก ์ฌ์ฉํ๋ ๊ฒฝ์ฐ

### ์ฌ๋ณผ๋ฆญ ๋งํฌ ์์ฑํ๊ธฐ
์ฌ๋ณผ๋ฆญ ๋งํฌ๋ `ln -s` ๋ก ์์ฑํ  ์ ์์ต๋๋ค. 
```bash
ln -s [ORIGINAL] [LINK]
```
`original.txt` ํ์ผ์ ๋ง๋ค๊ณ  ํด๋น ํ์ผ์ ๋ํ ์ฌ๋ณผ๋ง ๋งํฌ `symbolic_link.txt` ๋ฅผ ๋ง๋ค์ด๋ด์๋ค!
```bash
user@e578ae40797b:~/inode_test# vim original.txt
user@e578ae40797b:~/inode_test# ln -s original.txt symbolic_link.txt
```
inode๋ฅผ ํ์ธํด๋ณด๋ฉด `original.txt` ์ `symbolic_link.txt` ์ inode ๋ฒํธ๊ฐ ๋ค๋ฅธ ๊ฒ์ ์ ์ ์์ต๋๋ค. ์๋ณธ๊ณผ ์ฐ๊ฒฐ๋ ์๋ก์ด ํ์ผ์ ๋ง๋๋ ๊ฒ์ด๋ผ์ ์๋ก์ด inode๊ฐ ์์ฑ๋๊ธฐ ๋๋ฌธ์ด์ฃ .
```bash
user@e578ae40797b:~/inode_test$ ls -ali
total 12
207131 drwxrwxr-x 2 user user 4096 Jun 19 17:19 .
207103 drwxr-xr-x 4 user user 4096 Jun 19 17:19 ..
207135 -rw-rw-r-- 1 user user   26 Jun 19 17:19 original.txt
207133 lrwxrwxrwx 1 user user   12 Jun 19 17:19 symbolic_link.txt -> original.txt
```
๊ทธ๋ฆผ์ผ๋ก ์ดํด๋ณด๋ฉด ์๋์ ๊ฐ์ ๊ตฌ์กฐ์๋๋ค.

![symbolic link](posts/2022-10-05-symbolic-link.svg)

### ์ฌ๋ณผ๋ฆญ ๋งํฌ ์์ ํ๊ธฐ
```bash
user@e578ae40797b:~/inode_test$ cat original.txt
This is the original file
user@e578ae40797b:~/inode_test$ cat symbolic_link.txt
This is the original file
```
์ฌ๋ณผ๋ฆญ ๋งํฌ๋ ์๋ณธ ํ์ผ์ ๊ฐ๋ฆฌํค๊ณ  ์๊ธฐ ๋๋ฌธ์ ๋ ํ์ผ์ ์กฐํํด๋ณด๋ฉด ๋ด์ฉ์ ๊ฐ์ต๋๋ค. ๊ทธ๋ ๋ค๋ฉด ์ฌ๋ณผ๋ฆญ ๋งํฌ ํ์ผ์ ์์ ํ๋ค๋ฉด ์ด๋ป๊ฒ ๋ ๊น์?
```bash
user@e578ae40797b:~/inode_test$ vim symbolic_link.txt
user@e578ae40797b:~/inode_test$ cat symbolic_link.txt
This is the symbolic link file
user@e578ae40797b:~/inode_test$ cat original.txt
This is the symbolic link file
```
`symbolic_link.txt` ๋ฅผ ์์ ํ์ง๋ง ๊ฒฐ๊ตญ ํด๋น ํ์ผ์ ์๋ณธ ํ์ผ์ ๊ฐ๋ฆฌํค๊ณ  ์๊ธฐ ๋๋ฌธ์ ์๋ณธ ํ์ผ์ด ์์ ๋ฉ๋๋ค.

### ์ฌ๋ณผ๋ฆญ ๋งํฌ ์ญ์ ํ๊ธฐ
์ฌ๋ณผ๋ฆญ ๋งํฌ๋ ์๋ณธ๊ณผ ๋ค๋ฅธ ํ์ผ์ด๊ธฐ ๋๋ฌธ์ ์ฌ๋ณผ๋ฆญ ๋งํฌ๋ฅผ ์ญ์ ํ๋๋ผ๋ ์๋ณธ ํ์ผ์๋ ์ํฅ์ ์ฃผ์ง ์์ต๋๋ค. 
```bash
user@e578ae40797b:~/inode_test# rm symbolic_link.txt
user@e578ae40797b:~/inode_test# cat original.txt
This is the original file
```
ํ์ง๋ง ์๋ณธ ํ์ผ์ ์ญ์ ํ๋ฉด ๋ฐ๋ก๊ฐ๊ธฐ์ฒ๋ผ ์ฌ๋ณผ๋ฆญ ๋งํฌ๋ ์ฌ์ฉํ  ์ ์๊ฒ ๋ฉ๋๋ค.
```bash
user@e578ae40797b:~/inode_test# rm original.txt
user@e578ae40797b:~/inode_test# cat symbolic_link.txt
cat: symbolic_link.txt: No such file or directory
```

## ํ๋ ๋งํฌ (hard link)
ํ๋ ๋งํฌ๋ ์๋ณธ ํ์ผ๊ณผ ๋์ผํ ํ์ผ์ด๋ฉฐ, ์๋ณธ ํ์ผ์ด ์ญ์ ๋๋๋ผ๋ ๋์ผํ ๋ด์ฉ์ ํ์ผ์ ๊ฐ์ง๊ณ  ์์ผ๋ฏ๋ก ์์์ ๊ณต์ ํ๋ฉด์๋ ๋ฐ์ดํฐ๋ฅผ ์์ ํ๊ฒ ๊ด๋ฆฌํ๊ณ ์ ํ  ๋ ์ฌ์ฉ๋ฉ๋๋ค. [^1] ํ๋๋งํฌ๋ ์ฌ๋ณผ๋ฆญ ๋งํฌ์ ๋ฌ๋ฆฌ ๋๋ ํ ๋ฆฌ๋ฅผ ๋งํฌํ  ์ ์์ต๋๋ค. [^2]

#### ํ๋ ๋งํฌ ์์ฑํ๊ธฐ
ํ๋ ๋งํฌ๋ `ln` ์ผ๋ก ์์ฑํ  ์ ์์ต๋๋ค.
```bash
ln [ORIGINAL] [LINK]
```
`original.txt` ํ์ผ์ ๋ง๋ค๊ณ  ํด๋น ํ์ผ์ ๋ํ ์ฌ๋ณผ๋ง ๋งํฌ `hard_link.txt` ๋ฅผ ๋ง๋ค์ด๋ด์๋ค!

```bash
user@e578ae40797b:~/inode_test$ vim original.txt
user@e578ae40797b:~/inode_test$ ln original.txt hard_link.txt
```
ํ๋ ๋งํฌ๋ ์๋ณธ ํ์ผ๊ณผ ๋์ผํ ํ์ผ์ด๊ธฐ ๋๋ฌธ์ ๋์ผํ inode๋ฅผ ๊ฐ์ง๋ฉฐ, ๊ถํ, ์์ ์ ๋ฑ ๋ชจ๋  ์ ๋ณด๊ฐ ๊ฐ์ต๋๋ค.
```bash
user@e578ae40797b:~/inode_test$ ls -ali
total 16
207131 drwxrwxr-x 2 user user 4096 Jun 19 22:00 .
207103 drwxr-xr-x 4 user user 4096 Jun 19 17:22 ..
207135 -rw-rw-r-- 2 user user   26 Jun 19 17:22 hard_link.txt
207135 -rw-rw-r-- 2 user user   26 Jun 19 17:22 original.txt
```
๊ทธ๋ฆผ์ผ๋ก ์ดํด๋ณด๋ฉด ์๋์ ๊ฐ์ ๊ตฌ์กฐ์๋๋ค.

![hard link](posts/2022-10-05-hard-link.svg)

### ํ๋ ๋งํฌ ์์ ํ๊ธฐ
```bash
user@e578ae40797b:~/inode_test# cat original.txt
This is the original file
user@e578ae40797b:~/inode_test# cat hard_link.txt
This is the original file
```
ํ๋ ๋งํฌ๋ ์๋ณธ ํ์ผ ์์ฒด์ด๊ธฐ ๋๋ฌธ์ ํ๋ ๋งํฌ๋ฅผ ์์ ํ๋ฉด ์๋ณธ ํ์ผ๋ ์์ ๋ฉ๋๋ค.
```bash
user@e578ae40797b:~/inode_test# vim hard_link.txt
user@e578ae40797b:~/inode_test# cat hard_link.txt
This is the hard link file
user@e578ae40797b:~/inode_test# cat original.txt
This is the hard link file
```

### ํ๋ ๋งํฌ ์ญ์ ํ๊ธฐ
์ฌ๋ณผ๋ฆญ ๋งํฌ์ ๋ฌ๋ฆฌ ํ๋ ๋งํฌ๋ ์๋ณธ ํ์ผ์ ์ญ์ ํ๋๋ผ๋ ํ๋ ๋งํฌ๊ฐ ํด๋น inode์ ๋ํ ์ ๋ณด๋ฅผ ๊ฐ์ง๊ณ  ์๊ธฐ ๋๋ฌธ์ ๊ทธ๋๋ก ์ฌ์ฉํ  ์ ์์ต๋๋ค.
```bash
user@e578ae40797b:~/inode_test# rm original.txt
user@e578ae40797b:~/inode_test# cat hard_link.txt
This is the hard link file
```
๋ง์ฐฌ๊ฐ์ง๋ก ํ๋ ๋งํฌ๋ฅผ ์ญ์ ํ๋๋ผ๋ ์๋ณธ ํ์ผ์ inode ์ ๋ณด๋ฅผ ๊ฐ์ง๊ณ  ์๊ธฐ ๋๋ฌธ์ ๊ทธ๋๋ก ์ฌ์ฉํ  ์ ์์ต๋๋ค.
```bash
user@e578ae40797b:~/inode_test# rm hard_link.txt
user@e578ae40797b:~/inode_test# cat original.txt
This is the original file
```

## ์ ๋ฆฌ
์ฌ๋ณผ๋ฆญ ๋งํฌ์ ํ๋ ๋งํฌ์ ๊ฐ์ฅ ํฐ ์ฐจ์ด๋ ์๋ก์ด inode์ ์์ฑ ์ฌ๋ถ์ด๋ฉฐ, ๊ทธ์ ๋ฐ๋ผ ์ฉ๋, ์๋ณธ ํ์ผ์ด ์ญ์ ๋๋ ๊ฒฝ์ฐ ์ ๊ทผ ๊ฐ๋ฅ ์ฌ๋ถ ๋ฑ์ด ๋ฌ๋ผ์ง๋๋ค.


[^1]: [ํ๋๋งํฌ์ ์ฌ๋ณผ๋ฆญ ๋งํฌ์ ์ฐจ์ด์ ](http://www.metalpen.net/blog/?p=439) ๋ฅผ ์ฐธ๊ณ ํ์ฌ ์์ฑํ์ต๋๋ค.
[^2]: [์ ๋๋ ํฐ๋ฆฌ Hard link๋ฅผ ๋ง๋ค ์ ์๋์?](https://notes.harues.com/posts/why-hard-link-not-allowed-for-directory/) ์์ ์ด์ ๋ํ ์์ธํ ๋ด์ฉ์ ํ์ธํ  ์ ์์ต๋๋ค.