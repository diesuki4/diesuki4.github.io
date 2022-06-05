---
title: "제곱, 거듭제곱, 제곱근 / Square, Power, Square Root"
date: 2022-06-05 11:39:25 +0900
categories: 제곱 거듭제곱 제곱근 Square Root Power
---
Square는 제곱($$n^2$$) 을 뜻한다.
<br>

```
Mathf.Pow(n, 2);
```
<br>

Pow는 거듭제곱($$n^k$$) 을 뜻한다.
<br>

```
Mathf.Pow(n, k);
```
<br>

Square Root는 제곱근($$\sqrt{n}$$) 을 뜻한다.
<br>

```
Mathf.Sqrt(n);
```
<br><br>

벡터의 크기를 구할 때 피타고라스의 정리에 의해 각 요소들의 제곱을 사용하게 되는데, 두 벡터의 크기만을 비교할 때는 정확한 길이를 구할 필요가 없기 때문에 불필요한 제곱근 연산을 할 필요가 없습니다.

따라서, 크기 비교만을 필요로 할 때 성능 저하를 줄이기 위해 Vector3를 다음과 같이 활용할 수 있습니다.
<br>

```
// Vector3.sqrMagnitude는 벡터 크기의 제곱을 뜻한다.
if (v1.sqrMagnitude > v2.sqrMagnitude)
	Debug.Log("v1이 더 크다.");
else if (v1.sqrMagnitude < v2.sqrMagnitude)
	Debug.Log("v2가 더 크다.");
else
	Debug.Log("크기가 같다.");
```
<br>
