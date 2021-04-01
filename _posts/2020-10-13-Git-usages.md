---
title: "나혼자 Git, 이정도만 알면 된다."
date: 2020-10-13 20:34:00 +0900
categories: git 사용법 명령어
---
GUI가 지원되는 OS라면 그냥 Sourcetree나 Github Desktop을 쓰면 된다.
본인은 GUI가 쓸데없는 리소스를 잡아먹는게 싫어서 개발용 PC를 CLI로 설치했다.

Git 처음 설치후 해야할것
```
git config --global user.name "설정한 이름"
git config --global user.email "{Git 로그인 이메일}"
# 푸시시 로그인 이메일 입력 생략
git config --global credential.username "{Git 로그인 이메일}"
```

Curl과 Github API로 사용자 정보 바로 가져오기
```
echo $(curl -s 'https://api.github.com/users/{Git 아이디}' | python -c 'import sys, json; print(json.load(sys.stdin)["name"])')
```

user.name 자동 갱신
```
# 자동 실행 스케줄러 설정
crontab -e
# 자동 갱신 스케줄러 등록 (가장 마지막 줄에 추가, 매시간마다 갱신)
0 * * * * git config --global user.name "$(curl -s 'https://api.github.com/users/{Git 아이디}' | python -c 'import sys, json; print(json.load(sys.stdin)["name"])')"
```

현재 변경된 모든 파일 인덱싱
```
git add --all
```

시스템 에디터로 커밋 코멘트 작성
```
git commit
```

커맨드 라인에서 바로 커밋 코멘트 작성
```
git commit -m "요약" -m "첫째줄\n둘째줄\n셋째줄"
```

푸시
```
git push
```

.gitpush 라는 스크립트를 만들어놓으면 편함 (.gitignore에 추가해줄것)
```
#!/bin/sh
git add --all
git commit
git push
```

풀
```
git pull
```

클론 (다운로드)
```
git clone https://github.com/{아이디}/{레포지토리}.git
```

푸시시 자동 로그인 설정 (홈 디렉터리 .gitconfig에 비밀번호가 Plain text로 저장되므로 사용하지 않는것이 좋음)
```
# 실행후 git push 등으로 한번 로그인해줘야 함
git config --global credential.helper store
```

브랜치 생성하기
```
# 기존 브랜치로 새 브랜치를 생성
git branch -m {기존 브랜치} {새 브랜치}
# 푸시
git push -u origin {새 브랜치}
```

브랜치 변경하기
```
# 이미 있는 브랜치로 변경할때
git checkout {브랜치}
# 새 브랜치를 생성하고 변경할때
git checkout -b {브랜치}
```

Default 브랜치 변경하기
```
# HEAD 가 {변경할 브랜치}를 가리키도록 변경
git symbolic-ref refs/remotes/origin/HEAD refs/remotes/origin/{변경할 브랜치}
# 브라우저로 Github 로그인 - 해당 레포지토리 - Settings - Branches 에서 Default 브랜치를 {변경할 브랜치}로 변경
```

브랜치 삭제하기
```
git push origin --delete {브랜치}
```

이미 푸시된 커밋 삭제하고 되돌리기
```
# 되돌릴 버전(커밋) 확인
git log
# 브랜치 확인
git branch
# 강제로 해당 버전으로 푸시 (+는 force)
git push origin +{커밋}:{브랜치}
# 로컬에서 작업한 내용은 보존
git reset {커밋}
# 로컬에서 작업한 내용까지 모두 되돌림
git reset --hard {커밋}
```

가장 최근 로컬 커밋 삭제 (작업한 내용은 보존)
```
git reset HEAD^
```

가장 최근 로컬 커밋 삭제 (작업한 내용까지 모두 되돌림)
```
git reset --hard HEAD^
```
