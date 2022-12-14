---
title: "๐ฝ(Embedded) Linux ์ปค๋ ์ด๋ฏธ์ง ์ข๋ฅ ๋ฐ mkimage"
author: minble
date: 2022-12-01 00:00:00 +0800
categories: [Embedded]
tags: [linux, kernel, mkimage]
---

32bit ARM ์์คํ์ ์ด๋ฏธ์ง ํฌ๊ธฐ, ROM/Flash/DRAM ์ฉ๋์ ๋ฏผ๊ฐํ์ฌ ์ปค๋ ์ด๋ฏธ์ง ์์ถ, ์์ฒด ์์ถ ํด์ , ์คํ ๋ฐ ์ฌ๋ฐฐ์น ๋ฐฉ๋ฒ ๋ฑ์ ๊ณ ๋ คํด ๋ค์ํ ๋ฐฉ๋ฒ์ผ๋ก ์ปค๋ ์ด๋ฏธ์ง๋ฅผ ์์ฑํฉ๋๋ค. 64bit ARM ์์คํ์ ๋น๊ต์  ์ฉ๋์ ์์ ๋ก์ ๋จ์ํ๊ณ  ๊ฐ๋จํ๊ฒ ์ปค๋ ์ด๋ฏธ์ง๋ฅผ ์์ฑํฉ๋๋ค.[^1]

## ์ปค๋ ์ด๋ฏธ์ง์ ์ข๋ฅ
์ปค๋ ์ด๋ฏธ์ง๋ ์ฌ์ฉ ๋ชฉ์ ์ ๋ฐ๋ผ ์ฌ๋ฌ ์ ํ์ผ๋ก ์์ฑ๋ฉ๋๋ค.

![kernel image](posts/2022-12-01-image.svg)

- `Image`: ์์ถํ์ง ์๊ณ  ์ฌ์ฉํ๋ ์ด๋ฏธ์ง
- `zImage`: (ARM32) ์์ฒด ์์ถ ํด์  ๋ฃจํด์ด ํฌํจ๋ ์์ถ๋ ์ด๋ฏธ์ง
- `Image.gz`: (ARM64) ์์ฒด ์์ถ ํด์  ๋ฃจํด์ด ํฌํจ๋ ์์ถ๋ ์ด๋ฏธ์ง
- `uImage`: U-Boot ํด์ธ mkimage๋ฅผ ์ด์ฉํด zImage์ 64byte ํค๋๋ฅผ ์ถ๊ฐํ U-boot์ฉ ์์ถ๋ ์ด๋ฏธ์ง

## mkimage
mkimage๋ U-Boot bootloader๊ฐ ์ฌ์ฉํ๋ ์ด๋ฏธ์ง๋ฅผ ์์ฑํ๋ ํด๋ก ์์ฑ๋ ์ด๋ฏธ์ง๋ linux kernel, device tree blob, root file system image, firmware image ๋ฑ์ ํฌํจํฉ๋๋ค. 

mkimage๋ ๋ ๊ฐ์ง ํ์์ ์ด๋ฏธ์ง๋ฅผ ์ง์ํฉ๋๋ค.

- `old`, `legacy` ํ์: kernel image, device tree blob, ramdisk image ๋ฑ์ concatenateํ๊ณ  64byte ํค๋๋ฅผ ์ถ๊ฐํ ์ด๋ฏธ์ง
- `new`, `FIT(Flattened Image Tree)` ํ์: ๋ค์ํ ์ด๋ฏธ์ง๋ฅผ ๋ณด๋ค ์ ์ฐํ๊ฒ ์ฒ๋ฆฌ, ๋ ๊ฐํ ์ฒดํฌ์ฌ์ผ๋ก ์ด๋ฏธ์ง ๋ฌด๊ฒฐ์ฑ ๋ณดํธ๋ฅผ ํฅ์์ํจ ์ด๋ฏธ์ง

์ฌ์ฉ ์ต์์ ์๋์ ๊ฐ์ต๋๋ค.[^2]

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
dumpimage๋ mkimage๋ก ๋น๋ํ ์ด๋ฏธ์ง๋ฅผ disassembleํ๋ ํด๋ก `-i` ์ต์์ mkimage์ `-d` ์ต์ ์์์ ๋ฐ๋๋ก ์ํํฉ๋๋ค.[^3]

## ์ ๋ฆฌ
[^1]: [์ปค๋์ด๋ฏธ์ง](http://jake.dothome.comkr/image1/) ๋ฅผ ์ฐธ๊ณ ํ์ฌ ์์ฑํ์ต๋๋ค.
[^2]: [Ubuntu Manpage: mkimage - Generate image for U-Boot](https://manpages.ubuntu.com/manpages/trusty/man1/mkimage.1.html) ๋ฅผ ์ฐธ๊ณ ํ์ฌ ์์ฑํ์ต๋๋ค.
[^3]: [Xilinx/u-boot-xlnx](https://github.com/Xilinx/u-boot-xlnx/blob/master/README) ๋ฅผ ์ฐธ๊ณ ํ์ฌ ์์ฑํ์ต๋๋ค.