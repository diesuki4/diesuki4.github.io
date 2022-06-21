---
title: "치트엔진 튜토리얼 게임즈 레벨 3 다양한 솔루션들 / Cheat Engine Tutorial Games Level 3 various solutions"
excerpt: "Cheat Engine 7.2 튜토리얼 게임즈를 푸는 다양한 방법"
date: 2021-03-30 18:39:00 +0900
categories: [Game Hacking]
tags: [치트엔진, 튜토리얼, 게임즈, 레벨 3, 솔루션]
---
치트엔진을 공부한지 어느덧 한달이 되었습니다.

재즐보프님의 튜토리얼을 보며 기초를 다졌고 지금은 Stephen Chapman의 채널을 보며 공부하고 있습니다.

먼저 지식을 나눔해주신 두 분께 감사하다는 말씀드리고 아래는 배운 내용들을 활용하여 치트엔진 튜토리얼 게임즈 레벨 3를 다양한 방법으로 클리어한 솔루션들입니다.

현 시간 기준 최신버전인 7.2버전을 기준으로 합니다.

<br><br>

무적 (Invincibility)

우선 플레이어의 X 좌표를 찾은 후 Memory Viewer로 충돌시 바뀌는 주변 값들을 확인합니다.

저는 0, 1로 바뀌는 플래그에 값을 쓰는 명령어를 찾은후 브레이크 포인트와 스택 트레이스를 활용해 스택 프레임을 올라가며 확인했습니다.

플레이어와 몬스터가 충돌한 후의 처리를 하는 부분에 브레이크 포인트를 걸면 충돌후 게임이 멈추게 되고 충돌 전까지는 멈추지 않습니다. 하지만, 플레이어와 몬스터의 충돌을 감시하는 명령은 계속해서 동작하고 있을것이고 스택 프레임을 올라가다 보면 충돌 전인데도 게임이 멈추는 부분이 있습니다.

그 부분이 바로 플레이어와 몬스터의 충돌을 감시하는 부분입니다.
```
[ENABLE]
"gtutorial-x86_64.exe"+41387:
  db EB 13 // 무조건 분기

[DISABLE]
"gtutorial-x86_64.exe"+41387:
  db 74 13 // 조건 분기
```

무한 점프 (Fly)

점프는 보통 0, 1의 플래를 가집니다.

점프와 일시정지 기능을 활용해 플래그를 찾아주면 됩니다.
```
[ENABLE]
"gtutorial-x86_64.exe"+41124:
  db 90 90 90 90 // 아무 작업 하지 않음

[DISABLE]
"gtutorial-x86_64.exe"+41124:
  db C6 43 7C 01 // 1로 플래그 세팅
```

무결성 검사 우회 (Bypass Integrity Check)

코드 인젝션이나 코드 패치 등이 일어났을때 타이틀에 Integrity check error라고 표시되는데 이 문자열을 찾은 후 어느 명령에서 사용되는지 확인합니다.

코드를 올라가보며 분기를 확인하면 됩니다.
```
[ENABLE]
"gtutorial-x86_64.exe"+3909A:
  db 90 90 // 아무 작업 하지 않음

[DISABLE]
"gtutorial-x86_64.exe"+3909A:
  db 74 20 // 조건 분기
```

모든 계단 클리어 (Clear All Stairs)

계단을 클리어할때 마다 초록색으로 바뀌는데 클리어한 계단 개수가 어딘가에 저장되어 있을것이고 그 값을 비교하고 있을것입니다.

값 검색으로 계단 개수가 저장된 주소를 확인하고 그 곳에 값을 쓰는 명령을 찾습니다.

코드 인젝션을 통해 바로 총 계단 개수인 12(C)를 쓰게 할 수도 있지만 이 값을 읽고있는(감시하는) 명령을 찾아 주변의 분기문을 확인해도 됩니다.
```
[ENABLE]
"gtutorial-x86_64.exe"+413F1:
  db 90 90 // 아무 작업 하지 않음

[DISABLE]
"gtutorial-x86_64.exe"+413F1:
  db 7C 19 // 조건 분기
```

출구로 순간이동 (Teleport to Exit)

플레이어의 (X, Y) 좌표를 검색을 통해 알아내고 이 값에 접근하는 명령에 코드 인젝션을 수행합니다.

```
[ENABLE]
alloc(newmem, 128, "gtutorial-x86_64.exe"+40F39)
label(return)

newmem:
  mov [rax+24], (float)0.97 // X 좌표
  mov [rax+28], (float)0.878 // Y 좌표
  jmp return

"gtutorial-x86_64.exe"+40F39:
  jmp newmem
  nop
return:

[DISABLE]
"gtutorial-x86_64.exe"+40F39:
  movss [rax+28], xmm9
dealloc(newmem)
```

몬스터 고정 (Freeze Monsters)

플레이어의 좌표를 구한것과 비슷하게 몬스터의 좌표를 구합니다.

몬스터의 좌표에 값을 쓰는 명령을 모두 찾아 NOP 처리합니다.

이 게임의 흥미로운 점은 플레이어와 몬스터의 좌표를 설정하는 코드를 공유하지 않는다는 것과 계단 클리어후 몬스터들이 출구로 이동할때 조차 몬스터마다 다른 코드를 사용한다는 점입니다.

```
[ENABLE]
"gtutorial-x86_64.exe"+42545:
  db 90 90 90 90 90
"gtutorial-x86_64.exe"+40BAD:
  db 90 90 90 90 90
"gtutorial-x86_64.exe"+40CCF:
  db 90 90 90 90 90
"gtutorial-x86_64.exe"+40DDB:
  db 90 90 90 90 90
"gtutorial-x86_64.exe"+40BFF:
  db 90 90 90 90 90
"gtutorial-x86_64.exe"+40E17:
  db 90 90 90 90 90
"gtutorial-x86_64.exe"+40D37:
  db 90 90 90 90 90
"gtutorial-x86_64.exe"+40D67:
  db 90 90 90 90 90
"gtutorial-x86_64.exe"+40E41:
  db 90 90 90 90 90
"gtutorial-x86_64.exe"+40CDC:
  db 90 90 90 90 90 90
"gtutorial-x86_64.exe"+40DE8:
  db 90 90 90 90 90 90

[DISABLE]
"gtutorial-x86_64.exe"+42545:
  movss [rax+24], xmm1
"gtutorial-x86_64.exe"+40BAD:
  movss [rax+24], xmm6
"gtutorial-x86_64.exe"+40CCF:
  movss [rax+24], xmm6
"gtutorial-x86_64.exe"+40DDB:
  movss [rax+24], xmm6
"gtutorial-x86_64.exe"+40BFF:
  movss [rax+24], xmm0
"gtutorial-x86_64.exe"+40E17:
  movss [rax+24], xmm0
"gtutorial-x86_64.exe"+40D37:
  movss [rax+24], xmm0
"gtutorial-x86_64.exe"+40D67:
  movss [rax+28], xmm0
"gtutorial-x86_64.exe"+40E41:
  movss [rax+28], xmm8
"gtutorial-x86_64.exe"+40CDC:
  movss [rax+28], xmm10
"gtutorial-x86_64.exe"+40DE8:
  movss [rax+28], xmm10
```

즉시 클리어 (Direct Clear)

게임을 클리어하게 되면 well done이라는 메세지 후에 You have beaten all ~ 이런 메세지가 출력됩니다. 

well done 은 1, 2단계에서도 나오는 메세지라서 You have beaten ~ 이 문자열을 검색해야 하는데 게임을 클리어하고 메세지가 출력될 때 문자열이 사용되므로 메세지 박스가 나온 후에 참조된 문자열에서 확인해주면 됩니다.

해당 문자열이 사용된 명령을 찾은후 스택 프레임을 올라가며 well done 문자열이 사용된 부분도 찾고 더 올라가 분기 조건을 확인해주면 됩니다.

모든 계단을 클리어했는지와 플레이어가 출구에 있는지를 확인하는 두 개의 분기가 사용됩니다.
```
[ENABLE]
"gtutorial-x86_64.exe"+4142C:
  test ax, 0 // ZF를 항상 1로 세팅
"gtutorial-x86_64.exe"+41453:
  db 90 90 // 아무 작업 하지 않음

[DISABLE]
"gtutorial-x86_64.exe"+4142C:
  cmp byte ptr [rax+18], 00 // 0과 비교
"gtutorial-x86_64.exe"+41453:
  db 74 5F // 조건 분기
```
