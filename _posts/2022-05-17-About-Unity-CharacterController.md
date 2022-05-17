---
title: "Unity CharacterController에 대하여 / About Unity CharacterController"
date: 2022-05-17 22:23:06 +0900
categories: Unity CharacterController 유니티 캐릭터 컨트롤러
---
CharacterController 는 캐릭터가 움직이는데 무거운 Rigidbody 컴포넌트를 사용할 필요가 없기 때문에 가볍게 캐릭터 조작 전용으로 만들어진 컴포넌트입니다.

Capsule Collider 를 내장하고 있어 움직이는 Collider 라고 생각하면 편하고 중력 등 간단한 기능들은 직접 구현하여 사용할 수 있도록 Rigidbody 의 무거운 물리 처리들은 빠져있습니다.

따라서, 충돌이 발생할 때도 힘을 받아 튕겨지는 Rigidbody 와 달리 물리적인 충돌 처리는 하지 않게 됩니다.

[CharacterController 의 충돌 처리 참고](https://diesuki4.github.io/unity/%EC%9C%A0%EB%8B%88%ED%8B%B0/%EC%B6%A9%EB%8F%8C/%EC%B2%98%EB%A6%AC/collision/handling/Unity-collision-handling/)
<br><br>

CharacterController 를 사용하더라도 Move() 함수가 아닌 transform.position 으로 이동을 제어하면 오브젝트를 통과하여 지나가게 됩니다.
<br><br>

또한, Rigidbody 처럼 매순간 물리적인 변화를 체크하지 않고 움직인 경우에만 변화된 값을 갱신합니다.

한 예로, Move(dir) 에서 dir의 크기가 0이어서 움직이지 않으면 collisionFlags 값이 null 로 반환되고 움직이는 동안에만 값이 갱신됩니다.
<br><br>

collisionFlags 는 다음과 같이 사용합니다.
```
if (GetComponent<CharacterController>().collisionFlags == CollisionFlags.Below)
{
    isJumping = false;
}
```

collisionFlags 는 실시간으로 감지하는 것이 아니라 Move() 함수의 결과로서 반영되므로 단순히 바닥이나 벽에 닿아있다고 해서 갱신되지는 않습니다.

점프 후 바닥에서 걸어다닌다고 할 때 착지 순간부터 y 방향으로의 이동량 없이 수평 이동량만 존재한다면 착지 순간에만 CollisionFlags.Below 가 됩니다.

따라서, 가만히 있더라도 중력으로 인해 y 방향에 값이 적용되고 있거나 벽에 붙어 있더라도 벽 방향으로 값이 적용되고 있으면 움직인 것으로 간주하니 주의해야 합니다.
<br><br>

OnCollision__() 처럼 CharacterController 에도 충돌한 오브젝트의 정보를 가져올 수 있는 콜백 함수가 존재합니다.

이 함수 역시 움직일 때만 Move() 함수의 결과로서 반영됩니다.
```
private void OnControllerColliderHit(ControllerColliderHit hit)
{
    print(hit.gameObject.name);
}
```
