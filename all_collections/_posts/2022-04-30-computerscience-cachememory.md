---
layout: post
title: "[ComputerScience] CPU와 캐시메모리!"
tags: 
categories:
  - ComputerScience
---

## Intro
cpu와 캐시메모리에 대해 알아보자

<br/>

## 1. CPU / 캐시메모리

![image](https://user-images.githubusercontent.com/51254582/166113683-b3b06d7d-9456-4bfe-8b9f-68eecdb3ac04.png)

 - CPU란 컴퓨터 시스템을 통제하고 프로그램의 연산을 실행·처리하는 가장 핵심적인 컴퓨터의 제어 장치이다.
 - 캐시메모리란 속도가 빠른 장치와 느린 장치 사이에서 속도 차에 따른 병목 현상을 줄이기 위한 범용 메모리이다.
 - 캐시메모리는 CPU 코어(고속)와 메모리(CPU에 비해 저속) 사이에서 속도 차에 따른 병목 현상을 완화하는 역할을 한다.
 - CPU가 주기억장치에서 저장된 데이터를 읽어올 때, 자주 사용하는 데이터를 캐시 메모리에 저장한 뒤, 다음에 이용할 때 주기억장치가 아닌 캐시 메모리에서 먼저 가져오면서 속도를 향상시킨다.

<br/>

## 2. 캐시메모리 구조


 - Cache는 Byte 보다 큰 Chunk인 Block 단위로 접근한다. Cache Line 하나 당 64Byte 를 사용한다.
 - 크게 사용하는 이유는, 매번 Memory를 access해서 data를 가져오는 것은 비용이 크므로 한번 memory에 접근할 때마다 한번에 많이 가져오는 것이다.
 - CPU가 데이터를 요청하여 캐시 메모리에 접근했을 때 캐시 메모리가 해당 데이터를 가지고 있다면 이를 '캐시 적중(cache hit)'이라고 부르고, 해당 데이터가 없어서 DRAM에서 가져와야 한다면 '캐시 부적중(cache miss)'라 부른다.
 - 캐시 메모리는 데이터 지역성(locality)의 원리를 사용하여 연속된 64Byte 의 data를 가져온다.
 
||`지역성`|`원리`|
|---|---|---|
|1|`시간 지역성`|for와 같은 반복문에 사용되는 조건 변수처럼 한번 참조된 데이터는 잠시후 참조될 가능성이 높다.|
|2|`공간 지역성`|a[0], a[1]처럼 같은 데이터 배열에 연속적으로 접근할때 참조된 데이터 근처에 있는 데이터가 잠시 후 사용될 가능성이 높다.|

<br/>

### 2.1 TLB(Translation Lookaside Buffer)

![image](https://user-images.githubusercontent.com/51254582/166133379-58db3931-3d1a-4bad-ae8c-a05601cd0506.png)

 - CPU로 부터 생성된 가상메모리 주소를 캐시메모리에 매핑하기 위해서는 Page Table 을 통해 가상메모리 주소를 물리메모리 주소로 바꾸어야 가능하다.
 - TLB는 CPU로 부터 전달받은 가상메모리 주소를 Page Table 을 통해 물리메모리 주소로 변환시키기 위한 Cache 이다.
  
![image](https://user-images.githubusercontent.com/51254582/166115157-444c5bc6-cc13-4665-addf-50e0659e5f17.png)
 
 - 현재의 컴퓨터 아키텍처에서 가상메모리 주소와 물리메모리 주소는 서로 Tag 만 불 일치하며 하위 12Bit 는 정확히 일치 한다. <br> 따라서 TLB 에서 메모리 주소를 찾을 때 가상메모리 주소의 하위 12Bit(Index + Offset) 를 Indexing 키로 사용한다.
 - Indexing 과정으로 찾아낸 물리메모리 주소를 가지고 캐시메모리에 접근한 뒤 주소의 하위 6Bit(Offset) 를 통해 필요한 캐시 데이터를 찾아낸다.
 
||`구성`|`설명`|
|---|---|---|
|1|`Tag`|Cache Block 안에서의 Cache Line 검색 키 <br> Index와 Offset이 같더라도 Tag가 다르면 Cache Miss 이다.|
|2|`Index`|Cache Block 검색 키|
|3|`Offset`|Cache Line 안에서의 데이터 검색 키|

 - [TIP] Indexing 구조를 고려하여 Cache Hit 가 높은 프로그래밍을 해야한다.
