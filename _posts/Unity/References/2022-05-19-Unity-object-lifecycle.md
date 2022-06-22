---
title: "Unity 오브젝트의 라이프 사이클 / Unity object lifecycle"
excerpt: "라이프 사이클 함수들의 실행 조건과 시기"
date: 2022-05-19 16:13:03 +0900
last_modified_at: 2022-06-22 21:35:06 +0900
categories: [Unity References]
tags: [Unity, 오브젝트, 라이프 사이클, Object, Lifecycle]
---
![unity-c-beginners-cheat-sheet](https://user-images.githubusercontent.com/79886133/169446204-3e73f24a-1b97-4281-9512-1da005ed006c.png)
<br>

Reset()
<br>

```
조건 : 에디터에서 게임 오브젝트에 스크립트 컴포넌트를 처음 추가했거나, 우클릭 - Reset 버튼을 눌렀을 때
실행 : 매번 호출
※ 에디터 모드에서만 실행되며, 플레이 모드에서는 아무 동작도 하지 않는다.
```
<br>

Awake()
<br>

```
조건 : 스크립트 컴포넌트의 활성화 여부와 상관 없이, 게임 오브젝트가 활성화 되면
실행 : 최초 1회만 호출
```
<br>

OnEnable()
<br>

```
조건 : 게임 오브젝트와 스크립트 컴포넌트가 둘 다 활성화 상태가 되면
실행 : 매번 호출되며, 최초 실행일 경우 Start() 보다 먼저 호출
```
<br>

Start()
<br>

```
조건 : OnEnable() 과 동일
실행 : 최초 1회만 호출
```
<br>

Update()
<br>

```
조건 : OnEnable() 과 동일
실행 : 매 프레임마다 호출
```
<br>

OnDisable()
<br>

```
조건 : 게임 오브젝트와 스크립트 컴포넌트가 둘 다 활성화 상태일 때, 둘 중 하나가 비활성화 되면
실행 : 매번 호출
```
<br>

OnDestroy()
<br>

```
조건 : 게임 오브젝트가 최소 1번 이상 활성화 상태였고, Destroy 가 발생하면
실행 : 1회만 호출
```
