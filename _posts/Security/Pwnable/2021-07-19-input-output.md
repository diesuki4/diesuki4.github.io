---
title: "포너블 입출력 정리 / pwnable IO"
excerpt: "파이썬으로 포너블 입출력하기"
date: 2021-07-19 13:55:00 +0900
last_modified_at: 2022-06-21 20:35:06 +0900
categories: [Pwnable]
tags: [포너블, 시스템 해킹, pwntools, sys, 입력, 출력, 입출력]
---
Dreamhack의 System Exploitation Fundamental 강의와 포너블 문제들을 풀며 익힌 포너블 입출력 방법을 정리한 문서입니다.
<br><br>

pwntools (바이트)
```
from pwn import *

p = process('./elf')

# 쓰기
p.write(b'\xef\xbe\xad\xde')

# 쓰기 + \n
p.writeline(p32(0xdeadbeef))

# 특정 문자열 이후에 쓰기
p.writeafter('\n', b'A'*100)

# 특정 문자열 이후에 쓰기 + \n
p.writelineafter('\n', 'Hello'.encode())

# 읽기
# 0xdeadbeef
addr = int(p.read(10), 16)

# 한 줄 읽기
# Hello World !!
bytes = p.readline()

# 특정 문자열까지 읽기
# addr = 0xdeadbeef
p.readuntil('addr = ')
addr = int(p.read(10), 16)
```
<br>

sys
```
import sys

# 문자열 쓰기
sys.stdout.write('A'*100)

# 문자열 여러 줄 쓰기
lines = sys.stdin.readlines()
lines = []
lines.append('B'*20)
lines.append('\xef\xbe\xad\xde')
sys.stdout.writelines(lines)

# 바이트 쓰기
sys.stdout.buffer.write(b'\xef\xbe\xad\xde')

# 바이트 여러 줄 쓰기
lines = sys.stdin.buffer.readlines()
lines = []
lines.append(b'B'*20)
lines.append('Hello'.encode())
sys.stdout.buffer.writelines(lines)

# 문자열 읽기
sys.stdin.read(10)

# 문자열 한 줄 읽기
sys.stdin.readline()

# 문자열 여러 줄 읽기
sys.stdin.readlines()

# 바이트 읽기
sys.stdin.buffer.read(10)

# 바이트 한 줄 읽기
sys.stdin.buffer.readline()

# 바이트 여러 줄 읽기
sys.stdin.buffer.readlines()
```
