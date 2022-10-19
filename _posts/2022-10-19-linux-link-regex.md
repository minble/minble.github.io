---
title: "💻(Linux) Links and Regular Expression"
author: minble
date: 2022-10-19 00:00:00 +0800
categories: [Linux]
tags: [symbolic link, regex, regular expression, ln]
---

심볼릭 링크, 하드 링크에 대한 설명은 [💻(Linux) Symbolic links and hard links](https://minble.github.io/posts/linux-file-link/)를 참고하세요.

## 심볼릭 링크에서 정규 표현식 사용하기
```bash
root@docker-blog-ming:/test/link_test# ls -ali             
total 8
2872 drwxr-xr-x 2 root root 4096 Oct 19 12:18 .
2871 drwxr-xr-x 3 root root 4096 Oct 19 12:14 ..
2873 -rw-r--r-- 1 root root    0 Oct 19 12:18 test-original1
```
심볼릭 링크를 생성할 때, 파일 이름을 직접 지정하는 대신 `test-*`와 같이 정규 표현식을 이용하여 링크할 파일을 지정할 수 있습니다. 현재 해당 경로에 `test-*`에 맞는 파일은 한 개이기 때문에 아래와 같이 test-original1을 참조하는 test-link가 생성됩니다.
```bash
root@docker-blog-ming:/test/link_test# ln -s test-* test-link
root@docker-blog-ming:/test/link_test# ls -ali
total 8
2872 drwxr-xr-x 2 root root 4096 Oct 19 12:18 .
2871 drwxr-xr-x 3 root root 4096 Oct 19 12:14 ..
2875 lrwxrwxrwx 1 root root   14 Oct 19 12:18 test-link -> test-original1
2873 -rw-r--r-- 1 root root    0 Oct 19 12:18 test-original1
``` 

## 디렉토리를 이용해 여러 개의 링크 생성하기
`test-*`에 맞는 파일이 여러개 있다면 어떻게 될까요? 아래와 같이 test-orignal1, test-original2, test-original3이 존재할 때 `test-*`를 이용해 링크 생성을 시도하면 `ln: target 'test-link': No such file or directory`라는 메세지가 출력됩니다.
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
이 에러를 이해하려면 `ln` 명령어에 대한 이해가 필요합니다. `ln --help`을 치면 사용법을 확인할 수 있습니다.
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
### TARGET에 대한 링크 생성하기
`ln [OPTION]... [-T] TARGET LINK_NAME`

일반적으로 사용하는 형태는 첫 번째 형태로 하나의 `TARGET`에 대해 하나의 `LINK_NAME`를 지정합니다. 아래와 같이 test-original1에 대한 test-link를 생성할 수 있습니다.
```bash
root@docker-blog-ming:/test/link_test# ln -s test-original1 test-link
root@docker-blog-ming:/test/link_test# ls -ali
total 8
2872 drwxr-xr-x 2 root root 4096 Oct 19 12:48 .
2871 drwxr-xr-x 3 root root 4096 Oct 19 12:14 ..
2888 lrwxrwxrwx 1 root root   14 Oct 19 12:48 test-link -> test-original1
2873 -rw-r--r-- 1 root root    0 Oct 19 12:18 test-original1
```
### 다른 디렉토리에 있는 TARGET에 대한 링크 생성하기
`ln [OPTION]... TARGET`

`TARGET`이 다른 디렉토리에 있을 때 `LINK_NAME`를 지정하지 않으면 `TARGET`과 동일한 이름을 가진 링크가 현재 디렉토리에 생성됩니다.
```bash
root@docker-blog-ming:/test/link_test# cd directory/
root@docker-blog-ming:/test/link_test/directory# ln -s ../test-original1
root@docker-blog-ming:/test/link_test/directory# ls -ali
total 8
2888 drwxr-xr-x 2 root root 4096 Oct 19 12:52 .
2872 drwxr-xr-x 3 root root 4096 Oct 19 12:51 ..
2892 lrwxrwxrwx 1 root root   17 Oct 19 12:52 test-original1 -> ../test-original1
```
### 디렉토리를 이용해 여러 개의 링크 생성하기
`ln [OPTION]... TARGET... DIRECTORY`
`ln [OPTION]... -t DIRECTORY TARGET...`

`TARGET` 여러 개와 `DIRECTORY` 이름을 지정하면 `DIRECTORY` 내에 주어진 `TAGET`들에 대한 링크들이 생성됩니다. 
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
주의할 점은 `ln` 명령어는 파일 이름을 그대로 이어주기 때문에 `TARGET`의 경로를 지정해주지 않으면 링크가 제대로 생성되지 않습니다. 
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
이 경우 test-original1이 자기 자신을 참조하게 되고, 파일을 조회해보면 아래와 같이 `Too many levels of symbolic links`라는 메세지가 출력됩니다.
```bash
root@docker-blog-ming:/test/link_test/result-nopath# cat test-original1
cat: test-original1: Too many levels of symbolic links
```
터미널에서도 아래와 같이 링크가 깨진 것을 확인할 수 있습니다. 따라서 아래와 같이 절대 경로나 상대 경로로 `TARGET`의 경로를 표현해주어야 합니다.

![broken link](posts/2022-10-19-broken-link.png)


## 정규 표현식 치환하기
### 정규 표현식에 매칭되는 파일이 여러 개일 때
그렇다면 위에서 살펴봤던 상황에서는 왜 `ln: target 'test-link': No such file or directory`라는 메세지가 출력되었을까요? 우리가 명령어에 정규 표현식을 사용하면 정규 표현식은 명령어가 수행되는 과정에서 매칭되는 값을 찾아 치환됩니다. 현재 폴더에서 `test-*`에 매칭되는 파일은 test-original1, test-original2, test-original3가 됩니다.
```bash
root@docker-blog-ming:/test/link_test# ls -ali test-*
2873 -rw-r--r-- 1 root root 0 Oct 19 12:18 test-original1
2875 -rw-r--r-- 1 root root 0 Oct 19 12:38 test-original2
2883 -rw-r--r-- 1 root root 0 Oct 19 12:38 test-original3
```
따라서 `ln -s test-* test-link`는 `ln -s test-original1 test-original2 test-original3 test-link`로 치환되고 이 형태는 `ln` 명령어의 세 번째 형태인 `ln [OPTION]... TARGET... DIRECTORY`로 해석되어 `test-link`는 `DIRECTORY`로 간주됩니다. 하지만 현재 폴더에 `test-link`라는 디렉토리는 없기 때문에 `No such file or directory`라는 메세지가 출력됩니다. 실제로 해당 명령어를 수행해보면 동일한 결과가 출력됩니다.
```bash
root@docker-blog-ming:/test/link_test# ln -s test-original1 test-original2 test-original3 test-link
ln: target 'test-link': No such file or directory
```
정규 표현식을 활용하려면 아래와 같이 디렉토리를 생성하고 디렉토리 이름을 인자로 주면 됩니다.
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
이 때 상대 경로를 이용하게 되면 정규 표현식에 대한 치환은 현재 디렉토리에서 이루어지기 때문에 상위 폴더(`/test`)에 `test-*`에 매칭되는 파일이 없어 링크가 제대로 생성되지 않습니다.
```
root@docker-blog-ming:/test/link_test# ln -s ../test-* result-directory2
root@docker-blog-ming:/test/link_test# ls -ali result-directory2
total 8
3628 drwxr-xr-x 2 root root 4096 Oct 19 13:31  .
2872 drwxr-xr-x 6 root root 4096 Oct 19 13:31  ..
3633 lrwxrwxrwx 1 root root    9 Oct 19 13:31 'test-*' -> '../test-*'
```

![broken link2](posts/2022-10-19-broken-link2.png)

### 정규 표현식에 매칭되는 파일이 없을 때
그렇다면 정규 표현식에 매칭되는 값이 없다면 어떻게 될까요?
```bash
root@docker-blog-ming:/test/link_test# ls -ali
total 20
2872 drwxr-xr-x 5 root root 4096 Oct 19 13:07 .
2871 drwxr-xr-x 3 root root 4096 Oct 19 12:14 ..
2873 -rw-r--r-- 1 root root    0 Oct 19 12:18 test-original1
2875 -rw-r--r-- 1 root root    0 Oct 19 12:38 test-original2
2883 -rw-r--r-- 1 root root    0 Oct 19 12:38 test-original3
```
현재 폴더에서 `test2-*`에 매칭되는 파일은 없는데요, `test-*`에 대한 링크를 생성해보겠습니다.
```bash
root@docker-blog-ming:/test/link_test# ln -s test2-* test-link
root@docker-blog-ming:/test/link_test# ls -ali test-link
3623 lrwxrwxrwx 1 root root 7 Oct 19 13:25 test-link -> 'test2-*'
root@docker-blog-ming:/test/link_test# cat test-link
cat: test-link: No such file or directory
```
`test-*`에 매칭되는 파일이 없어 해당 정규 표현식은 치환되지 않고 문자열 그대로 들어가게 되고, `test2-*`라는 파일은 없기 때문에 조회해보면 `No such file or directory`라는 메세지가 출력되고 링크가 깨진 것을 확인할 수 있습니다.

![broken link2](posts/2022-10-19-broken-link3.png)