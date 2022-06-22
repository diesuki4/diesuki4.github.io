---
title: "Instantiate, Awake, Start 호출 순서 문제 / Instantiate, Awake, and Start call order issues"
excerpt: "Awake, Start 호출 순서에 따른 오동작"
date: 2022-05-20 12:45:06 +0900
last_modified_at: 2022-06-22 21:38:06 +0900
categories: [Unity Tips]
tags: [Instantiate, Awake, Start, 호출 순서, 문제, call order, issues]
---
```
// Enemy.cs
GameObject blt = Instantiate(bullet);
blt.transform.position = transform.position;
```

```
// Bullet.cs
void Awake()
{
    direction = (player.transform.position - transform.position).normalized;
}
```

Awake() 는 Instantiate() 직후에 호출되기 때문에 blt.transform.position = transform.position; 이전에 실행될 가능성이 매우 높습니다.

따라서, Awake() 의 transform.position 에는 Enemy 의 위치가 아니라 Bullet 프리팹의 초기 위치가 들어가므로 이상한 방향으로 총알이 날아가게 됩니다.
<br><br>

```
// Bullet.cs
void Start()
{
    direction = (player.transform.position - transform.position).normalized;
}
```

Start() 는 첫 번째 프레임의 업데이트 전에 호출되기 때문에 적어도 현재 실행중인 Enemy 의 Update() 가 끝난 이후에 호출됨을 보장할 수 있습니다.

blt.transform.position = transform.position; 으로 위치를 먼저 초기화한 후 Start() 에서 방향을 잡으므로 의도한대로 총알이 날아가게 됩니다.
