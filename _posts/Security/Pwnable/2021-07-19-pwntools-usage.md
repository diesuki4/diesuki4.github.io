---
title: "pwntools 사용법 정리 / pwntools usage"
excerpt: "유용한 활용법들"
date: 2021-07-19 12:37:00 +0900
last_modified_at: 2022-06-21 20:38:06 +0900
categories: [Pwnable]
tags: [포너블, 시스템 해킹, pwntools, 사용법]
---
Dreamhack의 System Exploitation Fundamental 강의와 포너블 문제들을 풀며 익힌 pwntools 사용법을 정리한 문서입니다.
<br><br>

pwntools 임포트
```
from pwn import *
```
<br>

로그 레벨 설정
```
context.log_level = 'debug'
```
<br>

실행 환경 설정
```
context(arch='i386', os='linux', bits='32')
```
<br>

바이너리 열기
```
# 바이너리
e = ELF('./elf')
```
<br>

프로세스 / nc / SSH 연결
```
# 프로세스
p = process('./elf')
# nc
r = remote('ctf.games.com', 12345)
# SSH
s = ssh('user', "ctf.games.com", 12345, 'password')
print(s['whoami'])
p = s.process("./elf")
```
<br>

인자 전달
```
arg = ['' for i in range(4)]
arg[0] = "0000"    # 변경 안 하면 실행 파일 경로
arg[1] = "AAA"
arg[2] = "\xef\xbe\xad\de"
arg[3] = "BB"

en = {}
en['LD_PRELOAD"] = './libc_name'
en['FLAG'] = 'flag'

# 프로세스
p = process('./elf', argv=arg, env=en)
# SSH
p = s.process('./elf', argv=arg, env=en)
```
<br>

보호 기법 정보
```
print(e.checksec())
```
<br>

올라간 주소, .text, .data, .bss 주소 확인
```
def addr_section(elf, name) :
    return elf.get_section_by_name(name).header.sh_addr

print(hex(e.address))
print(hex(addr_section(e, '.text')))
print(hex(addr_section(e, '.data')))
print(hex(addr_section(e, '.bss')))
```
<br>

함수 주소 검색
```
print(hex(e.symbols['system'])) 
print(hex(e.got['system'])) 
print(hex(e.plt['system'])) 
```
<br>

문자열 주소 확인
```
def search_str(elf, str, all = False) :
    if all :    # 모두 반환 (리스트)
        return list(elf.search(str.encode()))
    else :    # 첫번째만 반환
        return next(elf.search(str.encode()))

print(hex(search_str(e, 'Hello')))

# '/bin/sh' 주소 찾기
libc = ELF('/lib32/libc-2.23.so')
print(hex(search_str(libc, '/bin/sh')))
```
<br>

패킹 / 언패킹
```
# 바이트로 패킹
print(p32(0xdeadbeef))
# 정수로 언패킹
print(u32(b'\xef\xbe\xad\xde'))
```
<br>

프로세스 데이터 입출력 (바이트)
```
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

FSB 페이로드 생성
```
fs_offset = 11    # 포맷 스트링 오프셋, %11$x 와 동일
addr = 0xdeadbeef    # 덮어쓸 주소
val = 123    # 덮어쓸 값

payload = fmtstr_payload(fs_offset, {addr : val}) 
```
<br>

쉘을 실행하는 어셈블리 코드 생성
```
# 문자열 어셈블리 코드 (개행 구분)
shellcraft.sh()
shellcraft.execve('/bin/sh')
```
<br>

어셈블리 코드를 바이트 쉘코드로 변환
```
asm(shellcraft.sh())
```
<br>

쉘코드를 역어셈블링
```
# 문자열 어셈블리 코드 (개행 구분 + 추가 정보)
disasm(b'\xXX\xXX\xXX\xXX\xXX')
```
<br>

disasm(shellcode) 를 run_assembly(asmcode) 에 사용 가능하도록 변환
```
# 문자열 어셈블리 코드 반환 (개행 구분, 추가 정보 제거)
def process_disasm(dis, slice = 32) :
    inst = ''
    for line in dis.split('\n') :
        inst += line[slice:] + '\n'
    return inst

asmcode = process_disasm(disasm(b'\xXX\xXX\xXX\xXX\xXX'))
```
<br>

어셈블리어 실행
```
p = run_assembly(shellcraft.sh())
p = run_assembly(process_disasm(disasm(b'\xXX\xXX\xXX\xXX\xXX')))
```
<br>

상호작용 모드 실행
```
p.interactive()
```
