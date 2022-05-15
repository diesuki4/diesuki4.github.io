---
title: "Unity 충돌 처리 / Unity collision handling"
date: 2022-05-10 20:12:06 +0900
categories: Unity 유니티 충돌 처리 collision handling
---
원칙적으로 Rigidbody는 움직이는 오브젝트만 가져야 하고

움직이지 않는 오브젝트에서 isTrigger를 조정하여 충돌할지 감지만 할지 조절하게 됩니다.
<br><br>

공부하면서 어떤 때에 OnTriggerEnter() 가 호출되고 어떤 때에 OnCollisionEnter() 가 호출되는지 헷갈려서 모든 경우의 수를 고려해 직접 충돌시켜 보았습니다.

![bandicam 2022-05-10 19-47-12-504](https://user-images.githubusercontent.com/79886133/167611899-a16da938-4199-4b6b-bacc-6f2f456edeb4.png)
<br><br>

아래는 결과를 토대로 순서도를 만들어 본 것입니다.

![Untitled Diagram drawio](https://user-images.githubusercontent.com/79886133/167633955-1c7f1514-1a13-4f74-8b0a-bf1a998a0aaa.png)
<br><br>

다음은 OnTrigger__() 가 발생하기 위한 조건입니다.
```
1. 둘 다 Collider 를 갖고 있어야 한다.
2. 둘 중 하나 이상은 Rigidbody 를 갖고 있어야 한다.
3. 둘 중 하나 이상은 isTrigger 가 활성화 상태여야 한다.
```
<br>

OnCollision__() 의 발생 조건입니다.
```
1. 둘 다 Collider 를 갖고 있어야 한다.
2. 둘 중 하나 이상은 Rigidbody 를 갖고 있어야 한다.
3. 둘 다 isTrigger 가 비활성화 상태여야 한다.
4. Rigidbody 를 갖는 오브젝트가 하나 이상 움직여야 한다.
```
<br>

마지막 4번째 분기에서 No 로 가는 상황이 가장 헷갈리는 상황입니다.

이 경우는 충돌이 일어날 것 같지만 실제로는 그렇지 않습니다.
```
OnCollision__() 발생 조건에서 Rigidbody 를 갖는 오브젝트가 모두 움직이지 않을 때
```
<br>

정리하자면, 충돌 처리는 OnTrigger 가 주이고 OnCollision 을 예외적인 상황으로 생각하면 쉽습니다.

둘 다 Collider 를 갖고 하나 이상이 Rigidbody 를 갖고 하나 이상이 isTrigger 가 활성화 상태이면 OnTrigger__() 가 호출됩니다.

OnTrigger__() 조건과 비슷하지만 둘 다 isTrigger 가 비활성화이고 Rigidbody 를 갖는 오브젝트가 하나 이상 움직이면 OnCollision__()

나머지는 충돌 처리가 일어나지 않습니다.
<br><br>

또, Trigger 는 물체를 지나치면서 Trigger 만 발생하는 반면 Collision 은 isKinematic 이 활성화 돼있지 않으면 물리적인 충돌 처리도 하게 됩니다.

Collision 이 발생할 때 Rigidbody 를 가진 오브젝트 끼리 충돌하면 물리적인 처리에 따라 함께 움직이고

Rigidbody 를 가진 움직이는 오브젝트가 Collider 를 갖는 오브젝트와 충돌하면 정지하거나 일방적으로 밀리게 됩니다.
