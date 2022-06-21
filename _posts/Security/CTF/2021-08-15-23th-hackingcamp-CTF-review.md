---
title: "제23회 해킹캠프 CTF 후기 / 23th Hackingcamp CTF review"
excerpt: "첫 CTF 참가 소감"
date: 2021-08-15 21:32:00 +0900
last_modified_at: 2022-06-21 20:40:06 +0900
categories: [CTF]
tags: [제23회, 해킹캠프, CTF, 후기, 23th, Hackingcamp, CTF, review]
---
처음으로 제대로 참가해 본 CTF 입니다.

많이 기여는 못해서 Write-up 이라고는 못 하겠고 후기라고 하겠습니다.
<br><br>

저를 포함해 팀원 대부분이 CTF 가 처음이라 쩔쩔 매고 있었는데 모르는 사이 파바박 올라가 있는 점수..

알고 보니 숨은 고수 분이 계셔서 혼자서 다 풀고 계셨습니다.

잘 모르겠다고 한들 가만히 있기는 죄송하니 몇몇 문제들을 훑어봤는데 생각보다 풀만한 문제들도 있어 푼 문제들 후기를 적어봅니다.

버스 받으며 노력한 결과 3위를 했고 함께 해준 팀원들에게 감사하다는 말씀 올립니다 :)
<br><br>

PyQt5 로 만들어진 윈도우 바이너리의 리버싱
```
ID, PW 를 입력받아 맞으면 flag 를 띄워주는 로직이었습니다.
x64dbg 를 켜고 MessageBox 를 호출하는 부분들에 BP 를 걸어보았는데 걸리지 않았습니다.
포기하고 Cheat Engine 을 켜서 심볼을 확인해보니 QInputDialog 라는 입력창을 사용하는 듯 보여 BP 를 걸었으나 역시 걸리지 않았습니다.
힌트에 ID 값이 나와있어서 Cheat Engine 에서 ID 값을 검색하고 각 주소들에서 주변 값들을 보다보니 PW 값을 찾을 수 있었습니다.
```
<br>

안드로이드 어플의 숨겨진 flag 찾기
```
MainActivity 창을 제외한 SecretActivity 라는 숨겨진 창이 있고 이것을 활용해 firebase 에 저장된 flag 를 찾는 로직입니다.
apktool.bat Apk.apk 로 디컴파일 해줍니다.
SecretActivity 에서는 toast 에서 값을 표시해주지만 MainActivity 에서는 toast 에서 값을 표시해주지 않기 때문에 SecretActivity 와 이름과 내용들을 서로 바꿔주고
SecretActivity 에서 플래그 출력 부분의 문자열을 바꿔준 후
apktool b -f -d Apk 로 리컴파일 해줍니다.
플레이 스토어에서 ZipSigner 를 다운받아 서명해 주지 않으면 설치가 되지 않습니다.
3개의 플래그 중 1번째, 3번째를 확인하였고 팀원이 2번째 플래그를 찾아주어 해결하였습니다.
```
<br>

원본 비밀번호 찾기 1
```
crypt 함수로 1~90000 사이의 난수와 salt 를 여러 번 암호한 값을 주고 원본 난수를 찾는 문제였습니다.
팀원께서 salt 값을 알려주셨고 C로 1~90000 까지의 브루트 포싱을 만들어 해결하였습니다.
```
<br>

원본 비밀번호 찾기 2
```
B = 비밀번호 + A + 난수 (1~23) 는
비밀번호 = B - A - 난수 (1~23) 으로 바꿀 수 있습니다.
B 와 A 를 ASCII 로 변환하여 16진수로 빼준 후 팀원의 도움으로 https://gchq.github.io/CyberChef/ 사이트를 알게 되어
ROT47 과 From Hex 를 적용후 값을 조절하니 flag 를 얻을 수 있었습니다.
```
<br>

zip 비밀번호 크랙
```
칼리 리눅스에서 fcrackzip 과 rockyou.txt 사전을 활용해 빨리 찾을 수 있었습니다.
압축을 해제하니 또 하나의 긴 암호문이 나왔는데 팀원이 base58 디코딩 하여 URL 에 접속해 flag 를 얻었습니다.
```
<br>

User-Agent 에서의 SQL 인젝션
```
그냥 접속을 하면 봇으로 탐지되어 거부당하는 웹 페이지가 있었습니다.
크롬 개발자 도구를 활용해 User-Agent 부분을 1" or "1"="1 로 조작하여 우회할 수 있었습니다.
관리자 페이지에 접속 했으나 X-Forwarded-for 헤더를 조작해도 IP 화이트리스트를 우회할 수 없어 포기..
```
<br>

phpMyAdmin 취약점 공격
```
페이지 소스 보기를 하니 input 태그 안에 ID, PW 가 들어있었습니다.
로그인 후 phpMyAdmin 버전 4.8.1 인 것을 확인했고
https://www.exploit-db.com/exploits/44928 을 참고해 /tmp/flag 를 출력하는 php 실행문을 포함하는 select SQL 문을 실행시키고
http://pypMy.Admin/index.php?target=db_sql.php%253f/../../../../../../../../var/lib/php/sessions/sess_{세션}
으로 접속하여 flag 를 확인할 수 있었습니다.
```
