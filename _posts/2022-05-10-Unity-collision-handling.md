---
title: "Unity 충돌 처리 / Unity collision handling"
date: 2022-05-10 20:12:06 +0900
categories: Unity 유니티 충돌 처리 collision handling
---
공부하면서 어떤 때에 OnTriggerEnter() 가 호출되고 어떤 때에 OnCollisionEnter() 가 호출되는지 헷갈려서 모든 경우의 수를 고려해 직접 충돌시켜 보았습니다.

![bandicam 2022-05-10 19-47-12-504](https://user-images.githubusercontent.com/79886133/167611899-a16da938-4199-4b6b-bacc-6f2f456edeb4.png)
<br><br>

아래는 결과를 토대로 순서도를 만들어 본 것입니다.

![Untitled Diagram drawio](https://user-images.githubusercontent.com/79886133/167612267-3d254764-06aa-4fb1-9f2e-afd520783a8a.png)
<br><br>

다음은 OnTrigger__() 가 발생하기 위한 조건입니다.
```
1. 둘 다 Collider 를 갖고 있어야 한다.
2. 둘 중 하나 이상은 Rigidbody 를 갖고 있어야 한다.
3. 둘 중 하나 이상은 isTrigger 가 활성화 상태여야 한다.
```
<br>

OnCollision__() 은 특정한 하나의 상황에서만 발생합니다.
```
1. 둘 다 Collider 를 갖고 있어야 한다.
2. 둘 다 Rigidody 를 갖고 있어야 한다.
3. 둘 다 isTrigger 가 비활성화 상태여야 한다.
```
<br>

마지막 4번째 분기에서 No 로 가는 상황이 가장 헷갈리는 상황입니다.

이 경우는 충돌이 일어날 것 같지만 실제로는 그렇지 않습니다.
```
1. 둘 다 Collider 를 갖고 있고
2. 하나가 Rigidbody 를 갖고 있고
3. 둘 다 isTrigger 가 비활성화 상태일 때
```
<br>

정리하자면, 충돌 처리는 OnTrigger 가 주이고 OnCollision 을 예외적인 상황으로 생각하면 쉽습니다.

둘 다 Collider 를 갖고 하나 이상이 Rigidbody 를 갖고 하나 이상이 isTrigger 가 활성화 상태이면 OnTrigger__() 가 호출됩니다.

나머지 경우 중 OnCollision__() 의 한 가지 특수한 상황을 제외하고는 충돌 처리는 발생하지 않습니다.

또, Trigger 는 물체를 지나치면서 Trigger 만 발생하는 반면 Collision 은 isKinematic 이 활성화 돼있지 않으면 물체를 밀고 가게 됩니다.
