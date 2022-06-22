---
title: "Unity 충돌 처리 / Unity collision handling"
excerpt: "Collider, Rigidbody, CharacterController의 충돌 조건과 콜백 함수"
date: 2022-05-10 20:12:06 +0900
last_modified_at: 2022-06-22 21:52:06 +0900
categories: [Unity Character]
tags: [Unity, 유니티, 충돌 처리, Collision handling]
---
원칙적으로 움직이는 오브젝트는 Collider 와 Rigidbody 를 둘 다 가져야 하고

움직이지 않는 오브젝트는 Collider 만 갖게 됩니다.
<br><br>

정적 오브젝트는 움직이지 않기 때문에 서로 충돌할 일이 없습니다.

동적 오브젝트가 정적 오브젝트에 충돌할 때 OnTrigger__(), 동적 오브젝트끼리 충돌할 때 OnCollision__() 으로 처리하게 됩니다.
<br><br>

공부하면서 어떤 때에 OnTrigger__() 가 호출되고 어떤 때에 OnCollision__() 이 호출되는지 헷갈려서 모든 경우의 수를 고려해 직접 충돌시켜 보았습니다.

![bandicam 2022-05-10 19-47-12-504](https://user-images.githubusercontent.com/79886133/167611899-a16da938-4199-4b6b-bacc-6f2f456edeb4.png)
<br><br>

다음은 OnTrigger__() 가 발생하기 위한 조건입니다.
```
1. 둘 다 Collider 를 갖고 있어야 한다.
2. 둘 중 하나 이상은 Rigidbody 혹은 CharacterController 를 갖고 있어야 한다.
3. 둘 중 하나 이상은 isTrigger 가 활성화 상태여야 한다.
```

OnCollision__() 의 발생 조건입니다.
```
1. 둘 다 Collider 를 갖고 있어야 한다.
2. 움직이는 오브젝트 중 하나 이상은 Rigidbody 를 갖고 있어야 한다.
3. 둘 다 isTrigger 가 비활성화 상태여야 한다.
```
<br>

Trigger 는 물체를 통과하면서 Trigger 만 발생하는 반면 Collision 은 isKinematic 이 활성화 돼있지 않으면 힘을 받아 튕기는 등의 물리적인 충돌 처리도 하게 됩니다. 
