---
title: "Local forward 방향을 구하는 방법 / How to find the local forward direction"
excerpt: "글로벌이 아닌, 로컬 좌표계에서의 forward 벡터를 구하는 방법"
date: 2022-05-21 20:57:06 +0900
last_modified_at: 2022-06-22 21:47:06 +0900
categories: [Unity Math]
tags: [방향, 구하는 방법, Find, Local forward, Direction]
---
transform.forward 는 글로벌 좌표계에서의 forward 방향 단위 벡터를 뜻합니다.

따라서, 자식 오브젝트에서 transform.forward 를 하더라도 로컬 좌표계에서의 forward 방향을 뜻하지는 않습니다.
<br><br>

아래와 같은 방법으로 로컬 좌표계에서의 forward 방향 단위 벡터를 구할 수 있습니다.
<br>

```
transform.localRotation * Vector3.forward;
```
<br>

Quaternion * Vector3 연산은 사실 Vector3 벡터를 Quaternion 만큼 회전시킨 벡터입니다.
<br>

```
// v1 을 45도 y축 회전한다.
Vector3 v2 = Quaternion.Euler(0, 45, 0) * v1;
```
