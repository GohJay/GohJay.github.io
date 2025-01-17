---
layout: post
title: "[ComputerScience] 캐시무효화"
tags: 
categories:
  - ComputerScience
---

## Intro
캐시무효화에 대해 알아보자

<br/>

## 1. 캐시무효화

![image](https://user-images.githubusercontent.com/51254582/169682818-60f836b9-d720-42bf-978f-c1f7d4f52e50.png)

 - 오늘날의 CPU는 다중 코어 시스템으로 구성된다. 그리고 각 코어마다 L1, L2 캐시메모리가 존재한다.
 - 그렇다면 우리는 한 가지 의문점이 생겨야 한다. Core[0] 에서 L1 캐시에 쓰기를 진행하면 다른 코어의 L1 캐시에 변경된 값이 적용될까?
 - 정답은 적용되지 않는다. 한 코어에서 쓰기를 진행한다면 다른 코어에서는 알 수 없다는 것이다.
 - 캐시메모리간 동기화 문제를 해결하기 위해 캐시메모리는 데이터 쓰기를 할 때마다 Bus 통신을 통해 다른 코어의 캐시메모리의 해당 캐시라인을 무효화 시킨다.
 - 각 코어에서 찾는 데이터의 캐시가 무효화 된 상황도 Cache Miss로 볼 수 있다.
 - 캐시무효화 방식은 Write-Through 와 Write-Back 두 가지로 구분된다.
 
||`방식`|`설명`|
|---|---|---|
|1|`Write-Through`|값이 변경 되었을 경우 물리 메모리 까지 가서 값 변경<br/>Instruction Cache 에서 사용하는 방식|
|2|`Write-Back`|값이 변경 되었을 경우 L1 캐시메모리만 변경 한다.<br/>해당 캐시 라인이 제거 될 때 물리 메모리에 반영 된다.<br/>Data Cache 에서 사용하는 방식|
