---
title: "💻(Linux) Inode"
author: minble
date: 2022-10-06 00:00:00 +0800
categories: [Linux]
tags: [metadata, inode, file system]
---

## Metadata

"hello"라는 내용을 담은 `sample.txt` 파일이 존재할 때 이 파일은 파일 크기, 권한, 소유자, 그룹, 생성/접근/수정 정보 등 다양한 정보를 가지고 있습니다.[^1] 파일과 관련된 이러한 정보들을 메타데이터(metadata)라고 합니다.

![symbolic link](posts/2022-10-06-metadata.svg)

파일을 오른쪽 클릭하고 `Properties` > `Details` 탭에서 실제 내용을 확인할 수 있습니다.

![symbolic link](posts/2022-10-06-properties.png)

## File system

하드 드라이브와 같은 저장 장치는 파일을 저장하기 전 파티션이 파일 시스템으로 포맷됩니다. 리눅스 시스템에서 파티션은 주로 EXT4로 포맷됩니다. 그 외 파일 시스템으로는 B-Tree File System(BTRFS), Reiser File System, ZFS 등이 있습니다.

## Inode (Index Node)

![symbolic link](posts/2022-10-06-inode.svg)

Inode는 EXT4에서 파일의 메타데이터를 담고 있는 자료구조입니다. 파일 시스템 내에서 파일이나 디렉토리는 고유한 inode를 가지며 inode 번호를 통해 구분이 가능합니다. 사용자가 파일에 접근할 때 파일명을 사용하지만 내부적으로는 파일 이름이 디렉토리 테이블에 저장된 inode 번호에 매핑되고, 매핑된 inode 번호를 통해 해당 inode에 빠르게 접근할 수 있습니다. [^2] 따라서 inode는 파일을 빠르게 찾기 위해 정보를 순서대로 정리한 것이라고 할 수 있습니다.

### Inode 번호

![symbolic link](posts/2022-10-06-inode-table.svg)

파일 시스템에서 inode 번호는 1부터 시작합니다. 첫 10개의 inode는 시스템용으로 예약되어 있고 사용자 파일은 11번째 inode부터 메타데이터를 저장합니다. 모든 inode는 inode 테이블에 저장됩니다. 하나의 inode는 256바이트이며 리눅스에서는 한 파일의 모든 메타데이터를 256바이트에 저장할 수 있습니다. inode의 총 개수는 가용 공간과 파티션에 저장된 파일의 개수에 따라 달라집니다.

### Inode의 할당과 해제



inode의 할당 상태를 확인하기 위한 inode bitmap이라는 자료구조가 존재합니다.  

![symbolic link](posts/2022-10-06-inode-bitmap1.svg)

위 표와 같이 8비트의 inode bitmap에 1번 ~ 8번 inode의 할당 상태를 표시했을 때, 할당된 inode는 bitmap 1 값을 가집니다. bitmap 값이 0이면 해당 inode가 현재 쓰이지 않는다는 것을 의미합니다. 현재 4번 inode는 사용중입니다. 

![symbolic link](posts/2022-10-06-inode-bitmap2.svg)

4번 inode에 메타데이터가 저장되어 있는 파일을 지우게 되면 해당 bitmap 값도 0이 되고 다른 파일이 사용할 수 있게 됩니다.

### Inode 조회

파일에 해당하는 inode는 `stat` 명령어로 조회할 수 있습니다.

```bash
user@e578ae40797b:~/inode_test$ stat original.txt
  File: original.txt
  Size: 26              Blocks: 8          IO Block: 4096   regular file
Device: 86h/134d        Inode: 207133      Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1000/    user)   Gid: ( 1000/    user)
Access: 2021-06-19 17:07:10.010000000 +0900
Modify: 2021-06-19 17:07:10.010000000 +0900
Change: 2021-06-19 17:07:10.010000000 +0900
 Birth: -
```

- `Inode`: inode 번호
- `Access`: 파일 권한
- `Links`: 해당 파일의 링크 수
- `Uid`: 소유자 정보
- `Gid`: 그룹 정보

또는 `ls` 명령어를 이용해 간단하게 inode 번호를 조회할 수도 있습니다.

```bash
user@e578ae40797b:~/inode_test$ ls -i original.txt
207133 original.txt
```

## 정리

[^1]: [How Do Linux Inodes Work?](https://www.maketecheasier.com/how-do-linux-inodes-work/)를 참고하여 작성했습니다. 
[^2]: [리눅스 시스템의 아이노드(inode), 심볼릭 링크(Symbolic Link), 하드 링크(Hard Link)](https://koromoon.blogspot.com/2018/05/inode-symbolic-link-hard-link.html)를 참고하여 작성했습니다.