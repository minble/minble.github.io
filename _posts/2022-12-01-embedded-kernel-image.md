---
title: "💽(Embedded) Linux 커널 이미지 종류 및 mkimage"
author: minble
date: 2022-12-01 00:00:00 +0800
categories: [Embedded]
tags: [linux, kernel, mkimage]
---

32bit ARM 시스템은 이미지 크기, ROM/Flash/DRAM 용량에 민감하여 커널 이미지 압축, 자체 압축 해제, 실행 및 재배치 방법 등을 고려해 다양한 방법으로 커널 이미지를 생성합니다. 64bit ARM 시스템은 비교적 용량에 자유로워 단순하고 간단하게 커널 이미지를 생성합니다.[^1]

## 커널 이미지의 종류
커널 이미지는 사용 목적에 따라 여러 유형으로 생성됩니다.

![kernel image](posts/2022-12-01-image.svg)

- `Image`: 압축하지 않고 사용하는 이미지
- `zImage`: (ARM32) 자체 압축 해제 루틴이 포함된 압축된 이미지
- `Image.gz`: (ARM64) 자체 압축 해제 루틴이 포함된 압축된 이미지
- `uImage`: U-Boot 툴인 mkimage를 이용해 zImage에 64byte 헤더를 추가한 U-boot용 압축된 이미지

## mkimage
mkimage는 U-Boot bootloader가 사용하는 이미지를 생성하는 툴로 생성된 이미지는 linux kernel, device tree blob, root file system image, firmware image 등을 포함합니다. 

mkimage는 두 가지 형식의 이미지를 지원합니다.

- `old`, `legacy` 형식: kernel image, device tree blob, ramdisk image 등을 concatenate하고 64byte 헤더를 추가한 이미지
- `new`, `FIT(Flattened Image Tree)` 형식: 다양한 이미지를 보다 유연하게 처리, 더 강한 체크섬으로 이미지 무결성 보호를 향상시킨 이미지

사용 옵션은 아래와 같습니다.[^2]

|                             |                                                                                                                                                                                                                                                                                                                                      |
|-----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <b>List image information:</b>     |                                                                                                                                                                                                                                                                                                                                      |
| -l [uimage file name]       | mkimage lists the information contained in the header of an existing U-Boot image.                                                                                                                                                                                                                                                   |
| <b>Create old legacy image:</b>    |                                                                                                                                                                                                                                                                                                                                      |
| -A [architecture]           | Set architecture. Pass -h  as  the  architecture  to  see  the  list  of  supported architectures.                                                                                                                                                                                                                                   |
| -O [os]                     | Set operating system. bootm command of u-boot changes boot method by os type.  Pass -h as the OS to see the list of supported OS.                                                                                                                                                                                                    |
| -T [image type]             | Set image type.  Pass -h as the image to see the list of supported image type.                                                                                                                                                                                                                                                       |
| -C [compression type]       | Set compression type.  Pass -h as the compression to  see  the  list  of  supported compression type.                                                                                                                                                                                                                                |
| -a [load addess]            | Set load address with a hex number.                                                                                                                                                                                                                                                                                                  |
| -e [entry point]            | Set entry point with a hex number.                                                                                                                                                                                                                                                                                                   |
| -n [image name]             | Set image name to 'image name'.                                                                                                                                                                                                                                                                                                      |
| -d [image data file]        | Use image data from 'image data file'.                                                                                                                                                                                                                                                                                               |
| -x                          | Set XIP (execute in place) flag.                                                                                                                                                                                                                                                                                                     |
| <b>Create FIT image:</b>           |                                                                                                                                                                                                                                                                                                                                      |
| -c [comment]                | Specifies  a  comment  to be added when signing. This is typically a useful message which describes how the image was signed or some other useful information.                                                                                                                                                                       |
| -D [dtc options]            | Provide special options to the device tree compiler that  is  used  to  create  the image.                                                                                                                                                                                                                                           |
| -f [image tree source file] | Image tree source file that describes the structure and contents of the FIT image.                                                                                                                                                                                                                                                   |
| -F                          | Indicates  that  an  existing  FIT  image should be modified. No dtc compilation is performed and the -f flag should not be given.  This can be  used  to  sign  images with additional keys after initial image creation.                                                                                                           |
| -k [key_directory]          | Specifies  the  directory containing keys to use for signing. This directory should contain a private key file <name>.key  for  use  with  signing  and  a  certificate <name>.crt (containing the public key) for use with verification.                                                                                            |
| -K [key_destination]        | Specifies  a  compiled device tree binary file (typically .dtb) to write public key information into. When a private key is used to sign an  image,  the  corresponding public  key  is written into this file for for run-time verification. Typically the file here is the device tree binary used by CONFIG_OF_CONTROL in U-Boot. |
| -r                          | Specifies that keys used to sign the FIT are required. This means that they must be verified  for  the  image  to  boot.  Without this option, the verification will be optional (useful for testing but not for release).                                                                                                           |

### dumpimage
dumpimage는 mkimage로 빌드한 이미지를 disassemble하는 툴로 `-i` 옵션은 mkimage의 `-d` 옵션 작업을 반대로 수행합니다.[^3]

## 정리
[^1]: [커널이미지](http://jake.dothome.comkr/image1/) 를 참고하여 작성했습니다.
[^2]: [Ubuntu Manpage: mkimage - Generate image for U-Boot](https://manpages.ubuntu.com/manpages/trusty/man1/mkimage.1.html) 를 참고하여 작성했습니다.
[^3]: [Xilinx/u-boot-xlnx](https://github.com/Xilinx/u-boot-xlnx/blob/master/README) 를 참고하여 작성했습니다.