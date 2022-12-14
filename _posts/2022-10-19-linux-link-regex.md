---
title: "π»(Linux) Links and Regular Expression"
author: minble
date: 2022-10-19 00:00:00 +0800
categories: [Linux]
tags: [symbolic link, regex, regular expression, ln]
---

μ¬λ³Όλ¦­ λ§ν¬, νλ λ§ν¬μ λν μ€λͺμ [π»(Linux) Symbolic links and hard links](https://minble.github.io/posts/linux-file-link/)λ₯Ό μ°Έκ³ νμΈμ.

## μ¬λ³Όλ¦­ λ§ν¬μμ μ κ· ννμ μ¬μ©νκΈ°
```bash
root@docker-blog-ming:/test/link_test# ls -ali             
total 8
2872 drwxr-xr-x 2 root root 4096 Oct 19 12:18 .
2871 drwxr-xr-x 3 root root 4096 Oct 19 12:14 ..
2873 -rw-r--r-- 1 root root    0 Oct 19 12:18 test-original1
```
μ¬λ³Όλ¦­ λ§ν¬λ₯Ό μμ±ν  λ, νμΌ μ΄λ¦μ μ§μ  μ§μ νλ λμ  `test-*`μ κ°μ΄ μ κ· ννμμ μ΄μ©νμ¬ λ§ν¬ν  νμΌμ μ§μ ν  μ μμ΅λλ€. νμ¬ ν΄λΉ κ²½λ‘μ `test-*`μ λ§λ νμΌμ ν κ°μ΄κΈ° λλ¬Έμ μλμ κ°μ΄ test-original1μ μ°Έμ‘°νλ test-linkκ° μμ±λ©λλ€.
```bash
root@docker-blog-ming:/test/link_test# ln -s test-* test-link
root@docker-blog-ming:/test/link_test# ls -ali
total 8
2872 drwxr-xr-x 2 root root 4096 Oct 19 12:18 .
2871 drwxr-xr-x 3 root root 4096 Oct 19 12:14 ..
2875 lrwxrwxrwx 1 root root   14 Oct 19 12:18 test-link -> test-original1
2873 -rw-r--r-- 1 root root    0 Oct 19 12:18 test-original1
``` 

## λλ ν λ¦¬λ₯Ό μ΄μ©ν΄ μ¬λ¬ κ°μ λ§ν¬ μμ±νκΈ°
`test-*`μ λ§λ νμΌμ΄ μ¬λ¬κ° μλ€λ©΄ μ΄λ»κ² λ κΉμ? μλμ κ°μ΄ test-orignal1, test-original2, test-original3μ΄ μ‘΄μ¬ν  λ `test-*`λ₯Ό μ΄μ©ν΄ λ§ν¬ μμ±μ μλνλ©΄ `ln: target 'test-link': No such file or directory`λΌλ λ©μΈμ§κ° μΆλ ₯λ©λλ€.
```bash
root@docker-blog-ming:/test/link_test# ls -ali
total 8
2872 drwxr-xr-x 2 root root 4096 Oct 19 12:38 .
2871 drwxr-xr-x 3 root root 4096 Oct 19 12:14 ..
2873 -rw-r--r-- 1 root root    0 Oct 19 12:18 test-original1
2875 -rw-r--r-- 1 root root    0 Oct 19 12:38 test-original2
2883 -rw-r--r-- 1 root root    0 Oct 19 12:38 test-original3
root@docker-blog-ming:/test/link_test# ln -s test-* test-link
ln: target 'test-link': No such file or directory
```
μ΄ μλ¬λ₯Ό μ΄ν΄νλ €λ©΄ `ln` λͺλ Ήμ΄μ λν μ΄ν΄κ° νμν©λλ€. `ln --help`μ μΉλ©΄ μ¬μ©λ²μ νμΈν  μ μμ΅λλ€.
```bash
root@docker-blog-ming:/test/link_test# ln --help
Usage: ln [OPTION]... [-T] TARGET LINK_NAME
  or:  ln [OPTION]... TARGET
  or:  ln [OPTION]... TARGET... DIRECTORY
  or:  ln [OPTION]... -t DIRECTORY TARGET...
In the 1st form, create a link to TARGET with the name LINK_NAME.
In the 2nd form, create a link to TARGET in the current directory.
In the 3rd and 4th forms, create links to each TARGET in DIRECTORY.
...
```
### TARGETμ λν λ§ν¬ μμ±νκΈ°
`ln [OPTION]... [-T] TARGET LINK_NAME`

μΌλ°μ μΌλ‘ μ¬μ©νλ ννλ μ²« λ²μ§Έ ννλ‘ νλμ `TARGET`μ λν΄ νλμ `LINK_NAME`λ₯Ό μ§μ ν©λλ€. μλμ κ°μ΄ test-original1μ λν test-linkλ₯Ό μμ±ν  μ μμ΅λλ€.
```bash
root@docker-blog-ming:/test/link_test# ln -s test-original1 test-link
root@docker-blog-ming:/test/link_test# ls -ali
total 8
2872 drwxr-xr-x 2 root root 4096 Oct 19 12:48 .
2871 drwxr-xr-x 3 root root 4096 Oct 19 12:14 ..
2888 lrwxrwxrwx 1 root root   14 Oct 19 12:48 test-link -> test-original1
2873 -rw-r--r-- 1 root root    0 Oct 19 12:18 test-original1
```
### λ€λ₯Έ λλ ν λ¦¬μ μλ TARGETμ λν λ§ν¬ μμ±νκΈ°
`ln [OPTION]... TARGET`

`TARGET`μ΄ λ€λ₯Έ λλ ν λ¦¬μ μμ λ `LINK_NAME`λ₯Ό μ§μ νμ§ μμΌλ©΄ `TARGET`κ³Ό λμΌν μ΄λ¦μ κ°μ§ λ§ν¬κ° νμ¬ λλ ν λ¦¬μ μμ±λ©λλ€.
```bash
root@docker-blog-ming:/test/link_test# cd directory/
root@docker-blog-ming:/test/link_test/directory# ln -s ../test-original1
root@docker-blog-ming:/test/link_test/directory# ls -ali
total 8
2888 drwxr-xr-x 2 root root 4096 Oct 19 12:52 .
2872 drwxr-xr-x 3 root root 4096 Oct 19 12:51 ..
2892 lrwxrwxrwx 1 root root   17 Oct 19 12:52 test-original1 -> ../test-original1
```
### λλ ν λ¦¬λ₯Ό μ΄μ©ν΄ μ¬λ¬ κ°μ λ§ν¬ μμ±νκΈ°
`ln [OPTION]... TARGET... DIRECTORY`
`ln [OPTION]... -t DIRECTORY TARGET...`

`TARGET` μ¬λ¬ κ°μ `DIRECTORY` μ΄λ¦μ μ§μ νλ©΄ `DIRECTORY` λ΄μ μ£Όμ΄μ§ `TAGET`λ€μ λν λ§ν¬λ€μ΄ μμ±λ©λλ€. 
```bash
root@docker-blog-ming:/test/link_test# mkdir result-absolute
root@docker-blog-ming:/test/link_test# ln -s /test/link_test/test-original1 /test/link_test/test-original2 /test/link_test/test-original3 result-absolute
root@docker-blog-ming:/test/link_test# ls -ali result-absolute
total 8
2897 drwxr-xr-x 2 root root 4096 Oct 19 13:02 .
2872 drwxr-xr-x 3 root root 4096 Oct 19 13:01 ..
2898 lrwxrwxrwx 1 root root   30 Oct 19 13:02 test-original1 -> /test/link_test/test-original1
2899 lrwxrwxrwx 1 root root   30 Oct 19 13:02 test-original2 -> /test/link_test/test-original2
2900 lrwxrwxrwx 1 root root   30 Oct 19 13:02 test-original3 -> /test/link_test/test-original3
```
```bash
root@docker-blog-ming:/test/link_test# mkdir result-relative      
root@docker-blog-ming:/test/link_test# ln -s ../test-original1 ../test-original2 ../test-original3 result-relative
root@docker-blog-ming:/test/link_test# ls -ali result-relative
total 8
2892 drwxr-xr-x 2 root root 4096 Oct 19 13:07 .
2872 drwxr-xr-x 4 root root 4096 Oct 19 13:07 ..
2895 lrwxrwxrwx 1 root root   17 Oct 19 13:07 test-original1 -> ../test-original1
2896 lrwxrwxrwx 1 root root   17 Oct 19 13:07 test-original2 -> ../test-original2
2902 lrwxrwxrwx 1 root root   17 Oct 19 13:07 test-original3 -> ../test-original3
```
μ£Όμν  μ μ `ln` λͺλ Ήμ΄λ νμΌ μ΄λ¦μ κ·Έλλ‘ μ΄μ΄μ£ΌκΈ° λλ¬Έμ `TARGET`μ κ²½λ‘λ₯Ό μ§μ ν΄μ£Όμ§ μμΌλ©΄ λ§ν¬κ° μ λλ‘ μμ±λμ§ μμ΅λλ€. 
```bash
root@docker-blog-ming:/test/link_test# mkdir result-nopath
root@docker-blog-ming:/test/link_test# ln -s test-original1 test-original2 test-original3 result-nopath
root@docker-blog-ming:/test/link_test# ls -ali result-nopath
total 8
2903 drwxr-xr-x 2 root root 4096 Oct 19 13:08 .
2872 drwxr-xr-x 5 root root 4096 Oct 19 13:07 ..
2904 lrwxrwxrwx 1 root root   14 Oct 19 13:08 test-original1 -> test-original1
2905 lrwxrwxrwx 1 root root   14 Oct 19 13:08 test-original2 -> test-original2
2906 lrwxrwxrwx 1 root root   14 Oct 19 13:08 test-original3 -> test-original3
```
μ΄ κ²½μ° test-original1μ΄ μκΈ° μμ μ μ°Έμ‘°νκ² λκ³ , νμΌμ μ‘°νν΄λ³΄λ©΄ μλμ κ°μ΄ `Too many levels of symbolic links`λΌλ λ©μΈμ§κ° μΆλ ₯λ©λλ€.
```bash
root@docker-blog-ming:/test/link_test/result-nopath# cat test-original1
cat: test-original1: Too many levels of symbolic links
```
ν°λ―Έλμμλ μλμ κ°μ΄ λ§ν¬κ° κΉ¨μ§ κ²μ νμΈν  μ μμ΅λλ€. λ°λΌμ μλμ κ°μ΄ μ λ κ²½λ‘λ μλ κ²½λ‘λ‘ `TARGET`μ κ²½λ‘λ₯Ό ννν΄μ£Όμ΄μΌ ν©λλ€.

![broken link](posts/2022-10-19-broken-link.png)


## μ κ· ννμ μΉννκΈ°
### μ κ· ννμμ λ§€μΉ­λλ νμΌμ΄ μ¬λ¬ κ°μΌ λ
κ·Έλ λ€λ©΄ μμμ μ΄ν΄λ΄€λ μν©μμλ μ `ln: target 'test-link': No such file or directory`λΌλ λ©μΈμ§κ° μΆλ ₯λμμκΉμ? μ°λ¦¬κ° λͺλ Ήμ΄μ μ κ· ννμμ μ¬μ©νλ©΄ μ κ· ννμμ λͺλ Ήμ΄κ° μνλλ κ³Όμ μμ λ§€μΉ­λλ κ°μ μ°Ύμ μΉνλ©λλ€. νμ¬ ν΄λμμ `test-*`μ λ§€μΉ­λλ νμΌμ test-original1, test-original2, test-original3κ° λ©λλ€.
```bash
root@docker-blog-ming:/test/link_test# ls -ali test-*
2873 -rw-r--r-- 1 root root 0 Oct 19 12:18 test-original1
2875 -rw-r--r-- 1 root root 0 Oct 19 12:38 test-original2
2883 -rw-r--r-- 1 root root 0 Oct 19 12:38 test-original3
```
λ°λΌμ `ln -s test-* test-link`λ `ln -s test-original1 test-original2 test-original3 test-link`λ‘ μΉνλκ³  μ΄ ννλ `ln` λͺλ Ήμ΄μ μΈ λ²μ§Έ ννμΈ `ln [OPTION]... TARGET... DIRECTORY`λ‘ ν΄μλμ΄ `test-link`λ `DIRECTORY`λ‘ κ°μ£Όλ©λλ€. νμ§λ§ νμ¬ ν΄λμ `test-link`λΌλ λλ ν λ¦¬λ μκΈ° λλ¬Έμ `No such file or directory`λΌλ λ©μΈμ§κ° μΆλ ₯λ©λλ€. μ€μ λ‘ ν΄λΉ λͺλ Ήμ΄λ₯Ό μνν΄λ³΄λ©΄ λμΌν κ²°κ³Όκ° μΆλ ₯λ©λλ€.
```bash
root@docker-blog-ming:/test/link_test# ln -s test-original1 test-original2 test-original3 test-link
ln: target 'test-link': No such file or directory
```
μ κ· ννμμ νμ©νλ €λ©΄ μλμ κ°μ΄ λλ ν λ¦¬λ₯Ό μμ±νκ³  λλ ν λ¦¬ μ΄λ¦μ μΈμλ‘ μ£Όλ©΄ λ©λλ€.
```
root@docker-blog-ming:/test/link_test# mkdir result-directory
root@docker-blog-ming:/test/link_test# ln -s /test/link_test/test-* result-directory
root@docker-blog-ming:/test/link_test# ls -ali result-directory
total 8
3634 drwxr-xr-x 2 root root 4096 Oct 19 13:32 .
2872 drwxr-xr-x 7 root root 4096 Oct 19 13:32 ..
3636 lrwxrwxrwx 1 root root   30 Oct 19 13:32 test-original1 -> /test/link_test/test-original1
3637 lrwxrwxrwx 1 root root   30 Oct 19 13:32 test-original2 -> /test/link_test/test-original2
3638 lrwxrwxrwx 1 root root   30 Oct 19 13:32 test-original3 -> /test/link_test/test-original3
```
μ΄ λ μλ κ²½λ‘λ₯Ό μ΄μ©νκ² λλ©΄ μ κ· ννμμ λν μΉνμ νμ¬ λλ ν λ¦¬μμ μ΄λ£¨μ΄μ§κΈ° λλ¬Έμ μμ ν΄λ(`/test`)μ `test-*`μ λ§€μΉ­λλ νμΌμ΄ μμ΄ λ§ν¬κ° μ λλ‘ μμ±λμ§ μμ΅λλ€.
```
root@docker-blog-ming:/test/link_test# ln -s ../test-* result-directory2
root@docker-blog-ming:/test/link_test# ls -ali result-directory2
total 8
3628 drwxr-xr-x 2 root root 4096 Oct 19 13:31  .
2872 drwxr-xr-x 6 root root 4096 Oct 19 13:31  ..
3633 lrwxrwxrwx 1 root root    9 Oct 19 13:31 'test-*' -> '../test-*'
```

![broken link2](posts/2022-10-19-broken-link2.png)

### μ κ· ννμμ λ§€μΉ­λλ νμΌμ΄ μμ λ
κ·Έλ λ€λ©΄ μ κ· ννμμ λ§€μΉ­λλ κ°μ΄ μλ€λ©΄ μ΄λ»κ² λ κΉμ?
```bash
root@docker-blog-ming:/test/link_test# ls -ali
total 20
2872 drwxr-xr-x 5 root root 4096 Oct 19 13:07 .
2871 drwxr-xr-x 3 root root 4096 Oct 19 12:14 ..
2873 -rw-r--r-- 1 root root    0 Oct 19 12:18 test-original1
2875 -rw-r--r-- 1 root root    0 Oct 19 12:38 test-original2
2883 -rw-r--r-- 1 root root    0 Oct 19 12:38 test-original3
```
νμ¬ ν΄λμμ `test2-*`μ λ§€μΉ­λλ νμΌμ μλλ°μ, `test-*`μ λν λ§ν¬λ₯Ό μμ±ν΄λ³΄κ² μ΅λλ€.
```bash
root@docker-blog-ming:/test/link_test# ln -s test2-* test-link
root@docker-blog-ming:/test/link_test# ls -ali test-link
3623 lrwxrwxrwx 1 root root 7 Oct 19 13:25 test-link -> 'test2-*'
root@docker-blog-ming:/test/link_test# cat test-link
cat: test-link: No such file or directory
```
`test-*`μ λ§€μΉ­λλ νμΌμ΄ μμ΄ ν΄λΉ μ κ· ννμμ μΉνλμ§ μκ³  λ¬Έμμ΄ κ·Έλλ‘ λ€μ΄κ°κ² λκ³ , `test2-*`λΌλ νμΌμ μκΈ° λλ¬Έμ μ‘°νν΄λ³΄λ©΄ `No such file or directory`λΌλ λ©μΈμ§κ° μΆλ ₯λκ³  λ§ν¬κ° κΉ¨μ§ κ²μ νμΈν  μ μμ΅λλ€.

![broken link2](posts/2022-10-19-broken-link3.png)