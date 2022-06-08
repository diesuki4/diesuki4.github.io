---
title: "Vector3의 다양한 활용 / Various uses of Vector3"
date: 2022-06-05 21:24:23 +0900
categories: Vector3 다양한 활용 Various uses
---
벡터의 거리를 계산하는 방법
<br>

```
// 방법 1
float distance = Vector3.Distance(v1, v2);

// 방법 2
float length = Vector3.Magnitude(v2 - v1);
length = (v2 - v1).magnitude;

// 정확한 거리가 아닌, 크기만을 비교할 때
// 거리의 제곱을 사용해 성능 저하를 줄일 수 있다.
float squaredLength1 = v1.sqrMagnitude;
float squaredLength2 = Vector3.SqrMagnitude(v2);
```
<br>

두 벡터의 사잇각을 구하는 방법
<br>

```
// 방법 1
float degree = Vector3.Angle(v1, v2);

// 방법 2
// 내적을 이용해서도 각도를 구할 수 있다.
// e1 · e2 = |e1| * |e2| * cosθ = 1 * 1 * cosθ = cosθ
// θ = Arccos(e1 · e2)
float degree = Mathf.Acos(Vector3.Dot(v1.normalized, v2.normalized)) * Mathf.Rad2Deg;
```
<br>

Vector3 의 다양한 기능들
<br>

```
// 벡터 v1 을 (m, n, k)배 하는 방법
Vector3 newVector = Vector3.Scale(v1, new Vector3(m, n, k));

// 벡터의 크기를 제한
v = Vector3.ClampMagnitude(v, maxLength);

// 벡터 v를 XZ 평면에 정사영
Vector3 projXZVector = Vector3.ProjectOnPlane(v, Vector3.up);

// 벡터 v가 XY 평면에 반사된 벡터
Vector3 refXYVector = Vector3.Reflect(v, Vector3.forward);

// v1, v2를 둘 다 정규화하고, v2를 v1의 수직 방향으로 만든다.
// v2를 v1의 직교 평면에 정사영한 후, 둘 다 정규화한다고 생각하면 된다.
Vector3.OrthoNormalize(ref v1.vector, ref v2.vector);
```
<br>

벡터의 내적
<br>

```
// A · B = B · A = Ax * Bx + Ay * By + Az * Bz = |A| * |B| * cosθ
// (1) 내적의 결과는 스칼라이다.
// (2) 서로 수직인 두 벡터의 내적은 0 이다.
// (3) 평행한 두 벡터의 내적은 벡터 크기의 곱과 같다.
// (4) 서로 반대 방향인 두 벡터의 내적은 벡터 크기의 곱 * -1 과 같다.
// (5) 자기 자신과의 내적은 cosθ 가 1 이므로, 벡터 크기의 제곱이 된다.
// (6) 0 < 내적 이면, -90° < θ < 90°
// (7) 내적 < 0 이면, 90° < θ < 270°
// 계산 전에 두 벡터를 단위 벡터로 만들면, e1 · e2 = cosθ 가 된다.

float dot = Vector3.Dot(v1, v2);
dot = v1.x * v2.x + v1.y * v2.y + v1.z * v2.z
dot = v1.magnitude * v2.magnitude * Mathf.Cos(Vector3.Angle(v1, v2) * Mathf.Deg2Rad)

// v1의 각 요소를 v2 만큼 Scale 하여 합한 것과도 같다.
Vector3 v3 = Vector3.Scale(v1, v2);
dot = v3.x + v3.y + v3.z;

// 두 벡터의 방향이 유사할 수록(차이가 0°에 가까워질수록) cosθ 가 1 에 가까위지고,
// 사잇각이 커질수록(차이가 180°에 가까워질수록) -1 에 가까워진다.

// 두 벡터가 평행할 경우
// θ 가 0° 이므로 cosθ 는 1 이다.
// 평행한 두 벡터의 내적은 벡터 크기의 곱과 같다.
dot = v1.magnitude * v2.magnitude

// 두 벡터가 반대 방향일 경우
// θ 가 180° 이므로 cosθ 는 -1 이다.
// 반대 방향인 두 벡터의 내적은 벡터 크기의 곱 * -1 과 같다.
dot = v1.magnitude * v2.magnitude * -1
```
<br>

내적의 활용 1

적이 플레이어의 앞에 있는지, 뒤에 있는지 판별
<br>

```
// 플레이어의 앞 방향
Vector3 forward = transform.forward;
// 플레이어가 적을 바라보는 방향
Vector3 target = (enemy.transform.position - transform.position).normalized;

float dot = Vector3.Dot(forward, target);

if (0 < dot)
    Debug.Log("적이 앞에 있다.");
else if (dot < 0)
    Debug.Log("적이 뒤에 있다.");
else
    Debug.Log("적이 옆에 있다.");
```
<br>

내적의 활용 2

적이 같은 방향을 바라보고 있는지 판별
<br>

```
// 플레이어의 앞 방향
Vector3 forward = transform.forward;
// 적의 앞 방향
Vector3 tgtForward = enemy.transform.forward;

float dot = Vector3.Dot(forward, tgtForward);

// 시야각이 180° 라 가정
if (0 < dot)
    Debug.Log("적이 플레이어의 앞 기준 -90° ~ 90° 방향을 바라보고 있다.");
else if (dot < 0)
    Debug.Log("적이 플레이어의 앞 기준 90° ~ 270° 방향을 바라보고 있다.");
else
    Debug.Log("적이 플레이어의 앞 기준 -90° 혹은 90° 방향을 바라보고 있다.");
```
<br>

내적의 활용 3

플레이어의 시야각에 적이 들어왔는지 판별
<br>

```
// 플레이어의 시야각은 90°
float sight = 90f;
// 플레이어의 앞 방향
Vector3 forward = transform.forward;
// 플레이어가 적을 바라보는 방향
Vector3 target = (enemy.transform.position - transform.position).normalized;

float cosHalfSight = Mathf.cos(sight/2 * Mathf.Deg2Rad);
float dot = Vector3.Dot(forward, target);

if (cosHalfSight < dot)
    Debug.Log("적이 시야각 내에 있다.");
else if (dot < cosHalfSight)
    Debug.Log("적이 시야각 밖에 있다.");
else
    Debug.Log("적이 시야각 경계선에 있다.");
```
<br>

내적의 활용 4

벡터를 벡터에 정사영
<br>

```
// 벡터 v1을 벡터 v2에 정사영
// v1이 v2에 정사영된 벡터의 크기 = |v1| * cosθ
// 정사영된 벡터 = |v1| * cosθ * v2의 방향 벡터
Vector3 newVector = v1.magnitude * Vector3.Dot(v1.normalized, v2.normalized) * v2.normalized;
```
<br>

벡터의 외적
<br>

```
// A x B = (Ay * Bz - By * Az, Az * Bx - Bz * Ax, Ax * By - Bx * Ay)
// |A x B| = |A| * |B| * sinθ
// (1) 외적의 결과는 A, B 에 둘 다 수직인 AB 평면의 법선 벡터(Normal Vector)이다.
// (2) 외적은 교환 법칙이 성립하지 않는다.
// (3) 두 벡터의 방향이 같거나, 서로 반대 방향이면 외적은 0 벡터이다.
// (4) BxA 는 AxB 의 반대 방향 벡터이다.
// (5) A x B 의 크기는 A, B 가 만드는 평행 사변형의 넓이와 같다.
// (6) 외적 C 는 A, B 모두에 수직이므로 각각의 내적은 0 이다.
// sinθ 는 두 벡터가 수직일 때 1, 사잇각이 0° 과 180° 일 때 0 이다.
// 계산 전에 두 벡터를 단위 벡터로 만들면, |e1 x e2| = sinθ 가 된다.
```
<br>

외적의 활용 1

두 벡터 중 어떤 것이 수직에 더 가까운지 판별
<br>

```
Vector3 baseNormV = baseV.normalized;
Vector3 NormV1 = v1.normalized;
Vector3 NormV2 = v2.normalized;

float crossLength1 = Vector3.Cross(baseNormV, NormV1).magnitude;
float crossLength2 = Vector3.Cross(baseNormV, NormV2).magnitude;

if (crossLength1 < crossLength2)
    Debug.Log("v2가 수직에 더 가깝다.");
else if (crossLength2 < crossLength1)
    Debug.Log("v1이 수직에 더 가깝다.");
else
    Debug.Log("수직으로부터의 각도 차이가 같다.");
```
<br>

외적의 활용 2

적이 오른쪽에 있는지, 왼쪽에 있는지 판별

내적 시 cosθ 에 따라 양, 음이 바뀌기 때문이다.
<br>

```
Vector3 forward = transform.forward;
Vector3 target = (enemy.transform.position - transform.position).normalized;
float result = Vector3.Dot(Vector3.up, Vector3.Cross(target, forward));

if (0 < result) // 0 < cosθ
    Debug.Log("적이 오른쪽에 있다.");
else if (result < 0) // cosθ < 0
    Debug.Log("적이 왼쪽에 있다.");
```
