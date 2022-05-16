---
title: "Unity에서 라이트를 이용해 밝게 만들기 / How to make brighter using light in Unity"
date: 2022-05-16 9:50:06 +0900
categories: Unity 유니티 라이트 밝게 만들기 light make brighter
---
처음 맵을 가져왔을 때는 분명 밝았는데 라이트 설정을 수정하고 라이트맵을 구울수록 점점 어두워져서 원인을 찾아봤습니다.
<br><br>

맵을 처음 가져왔을 때 미리 구워져있던 라이트맵이 적용된 화면입니다.

![bandicam 2022-05-16 09-43-35-963](https://user-images.githubusercontent.com/79886133/168503559-a7dbba1e-0be1-497c-aca9-eaed1eccc3fa.png)
<br><br>

아래는 이것저것 라이트 설정을 수정한 화면입니다.
화면이 꽤 어두워진 게 눈에 보입니다.

![bandicam 2022-05-16 09-45-11-028](https://user-images.githubusercontent.com/79886133/168503573-af524dfa-0578-48e9-b88c-75c456de3ecb.png)
<br><br>

원인을 찾아보다가 라이트 매핑 설정에서 라이팅 모드를 Subtractive가 아닌 Shadowmask나 Baked Indirect로 구워주면 다시 밝아진다는 것을 확인했습니다.

![bandicam 2022-05-16 09-49-10-307](https://user-images.githubusercontent.com/79886133/168503594-3a2cb80a-fd56-4dcf-91c8-bb64f710d3b9.png)
<br><br>

라이트의 Intensity를 조정해서도 밝게 만들 수 있습니다.

![bandicam 2022-05-16 09-51-18-607](https://user-images.githubusercontent.com/79886133/168503601-62b7afc1-93bc-40ef-ad72-ebb0e31d5045.png)
<br><br>

라이트를 공부하며 정리한 내용입니다.
```
라이트 모드
- Realtime : 동적 오브젝트에만 영향을 미친다.
- Baked : 정적 오브젝트(라이트맵을 구울 때)에만 영향을 미친다.
- Mixed : 동적, 정적 오브젝트 모두에 영향을 미친다.
```
```
라이팅 모드
- Subtractive : GI, 직접광, 그림자를 모두 라이트맵에 구우며 퀄리티가 나쁘고 초저사양을 위한 최후의 보루이다.
- Baked Indirect : GI만 굽고 직접광과 그림자는 실시간으로 계산하며 성능 소요가 높다.
- Shadowmask : Baked Indirect와 비슷하지만 그림자를 쉐도우 마스크라는 텍스처로 미리 만들어 놓기 때문에 Baked Indirect보다 성능 소요는 낮지만 메모리가 더 필요하다.
```
