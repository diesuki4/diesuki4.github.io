---
title: "Ubuntu 포너블 환경 구축 방법 / Ubuntu pwnable setup"
excerpt: "Ubuntu 16.04 포너블 환경 구축하기"
date: 2021-07-19 14:39:00 +0900
last_modified_at: 2022-06-21 20:33:06 +0900
categories: [Pwnable]
tags: [Ubuntu, 우분투, 16.04, xenial, 64비트, amd64, pwnable, 포너블, 환경]
---
Ubuntu 16.04 (Xenial) 64비트 + Python 3 기준 포너블 공부 환경 구축 방법 정리입니다.
<br><br>

빌드 툴, 32비트 libc, GDB, NASM 설치
```
$ sudo su
# apt install build-essential libc6-dev-i386 gdb nasm
```
<br>

ROPGadget, pwntools 설치
```
# apt install python3-dev curl git
# pip install ropgadget
# pip install pwntools
```
<br>

OneGadget 설치
```
# apt install software-properties-common
# add-apt-repository ppa:brightbox/ruby-ng
# apt update && apt install ruby2.6
# git clone https://github.com/david942j/one_gadget.git
# cd one_gadget
# gem install one_gadget
# cd ..
# rm -rf one_gadget
```
<br>

hexyl 설치
```
# wget https://github.com/sharkdp/hexyl/releases/download/v0.9.0/hexyl_0.9.0_amd64.deb
# dpkg -i hexyl_0.9.0_amd64.deb
# rm -rf hexyl_0.9.0_amd64.deb
```
<br>

PEDA 설치 (유저별)
```
# exit
$ git clone https://github.com/longld/peda.git ~/peda
$ nano ~/peda/lib/utils.py
Line 114
COLORS = {blue": "34"} -> COLORS = {blue": "36"}
```
<br>

Pwngdb 설치 (유저별)
```
$ cd ~/
$ git clone https://github.com/scwuaptx/Pwngdb.git
$ cp ~/Pwngdb/.gdbinit ~/
```
<br>

GDB 설정 (유저별)
```
$ nano ~/.gdbinit
set disassembly-flavor intel
```
