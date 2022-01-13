---
title: "PE-bear 코드 패치 / PE-bear code patch"
date: 2022-01-03 23:33:06 +0900
categories: PE-bear 코드 패치 code patch
---
PE 구조를 확인하기 위해 널리 사용되는 PEview 와 비슷한 기능을 제공하는 PE-bear 라는 툴이 있습니다.

64비트 구조 지원 뿐만 아니라 .text 영역을 어셈블리어로 표시해주는 기능도 제공해 제가 애용하는 분석 도구입니다.
<br><br>

다만, 프로그램 실행시 RVA 뷰가 기본값이고 따로 설정이 저장되지 않아 프로그램을 켤 때마다 VA 로 변경해주어야 하는 번거로움이 있습니다.

이번 포스트에서는 PE-bear 의 실행시 뷰 기본값을 RVA 가 아닌 VA 로 설정해주는 코드 패치를 적용한 방법을 적어 보았습니다.
<br><br>

해당 값을 적용해주는 부분은 치트 엔진을 이용해 클릭했을 때 변하는 값을 검색하고 분석하여 찾아냈습니다.

0.5.3.2 버전에서는 .text 영역의 마지막 부분에 Null Padding 이 있어 간단하게 코드 케이브를 이용해 수정할 수 있었습니다.

하지만 0.5.5 버전에서는 .text 영역의 마지막 부분까지 알뜰하게 사용하고 있습니다.

![bandicam 2022-01-05 10-34-12-425](https://user-images.githubusercontent.com/79886133/148147073-58a7e2cd-8586-4c95-9f52-147991f1a4e0.png)
<br><br>

.data 영역의 마지막 Null Padding 부분에 코드 케이브를 시도했지만 메모리 엑세스 위반 오류 발생..

어쩔 수 없이 .text 영역의 사용하지 않는 부분을 찾아보던 중 CC (INT3 인터럽트) 가 연속적으로 있는 부분을 여러 줄 발견했습니다.

![bandicam 2022-01-05 10-34-00-987](https://user-images.githubusercontent.com/79886133/148148358-b2298549-55a5-407c-8a06-d78525561b64.png)
<br><br>

이 부분이 사용하지 않는 영역이라는 가정 하에 이 부분들에 코드 1개와 JMP 1개씩을 끼워넣어 코드 케이브를 시도했습니다.

![bandicam 2022-01-05 10-58-01-047](https://user-images.githubusercontent.com/79886133/148149176-75e408c9-5d06-4a99-a2bc-5e7f83b926bb.png)
![bandicam 2022-01-05 10-58-39-010](https://user-images.githubusercontent.com/79886133/148149184-c3322c77-f73d-4805-a456-7c38e055822c.png)
![bandicam 2022-01-05 10-59-15-000](https://user-images.githubusercontent.com/79886133/148149188-2ac6a714-4ea6-4ef5-9d3e-cc3011a5095c.png)
![bandicam 2022-01-05 10-59-28-933](https://user-images.githubusercontent.com/79886133/148149194-a7ae89b7-d82f-4a0b-8fcd-fc6300494664.png)
![bandicam 2022-01-05 11-00-02-944](https://user-images.githubusercontent.com/79886133/148149199-f2641f9a-0f63-4fcc-939f-4c89b8beb138.png)
<br><br>

대성공 !

성공적으로 기본값이 VA 로 변경되었습니다.

![bandicam 2022-01-05 11-08-22-130](https://user-images.githubusercontent.com/79886133/148149605-4db83730-040b-4280-a1aa-990363b10b04.png)
