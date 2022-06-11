---
layout: post
title: "[C/C++] 동적할당 방식의 차이!(malloc free, new delete)"
tags: 
categories:
  - Language
---

## Intro
동적할당 방식에 따른 공통점과 차이점을 자세히 알아보자

<br/>

## 1. malloc free / new delete 비교

 - 공통점 : heap 메모리 영역에 동적메모리를 할당한다.
 - 차이점 : 동적할당 대상이 객체일 경우 생성자, 소멸자 호출의 유무가 차이난다.

<br/>

### 1.1 malloc free

 - 먼저 malloc free 에 대해 디스어셈블러와 메모리뷰를 통해 분석해보자
 
![image](https://user-images.githubusercontent.com/51254582/169966752-fc31c16f-3689-4c14-b5ac-aee8572376ce.png)
![image](https://user-images.githubusercontent.com/51254582/169967131-5045896b-47df-4c3f-90c9-400ab4190dd3.png)
  
 - 어셈블리어 확인결과 malloc 과 free 과정에서 생성자와 소멸자의 호출 과정은 존재하지 않는다는것을 확인할 수 있다.
 - 정확하게 지정한 크기(int 4byte * size 5 = 20byte) 만큼만 동적할당이 된 것을 메모리뷰에서 확인할 수 있다.
 
<br/>

### 1.2 new delete

 - 다음으로 new delete 에 대해 디스어셈블러와 메모리뷰를 통해 분석해보자
 
![image](https://user-images.githubusercontent.com/51254582/169975079-497eccc0-752f-44cf-8a17-ed918535917d.png)
![image](https://user-images.githubusercontent.com/51254582/169974831-16a82eb2-9ef7-44d5-8931-6984cb814288.png)
![image](https://user-images.githubusercontent.com/51254582/169970097-2a41e822-c942-48a1-9608-b3ea9545476a.png)

 - 어셈블리어 확인결과 new 는 malloc 함수를 래핑하였다는 것을 알 수 있었다.
 - 그러나 new 는 malloc 과 달리 동적메모리 공간을 20byte가 아닌 24byte로 4byte 공간을 더 할당하였다. 왜 4byte를 더 할당하였을까?
 - 그에 대한 해답은 메모리 뷰와 delete[] 함수에서 찾을 수 있었다.
 - 메모리 뷰 분석 결과 동적할당을 요청한 20byte의 바로 위 4byte 공간에 동적배열 size를 기록한다는 것을 확인 할 수 있었고, 
 - delete[] 함수에 래핑된 'vector deleting destructor' 함수에서 이곳에 기록된 size 만큼 소멸자를 호출한다는 것을 알 수 있었다.
 - 그리고 new 가 malloc 을 래핑한 것과 같이 delete 또한 free 를 래핑한 함수라는 것을 알 수 있었는데, delete[]는 더 할당받은 주소 공간 만큼(24byte) free를 호출해 메모리를 해제해준다는 것을 분석을 통해 알 수 있었다.
 
<br/>

#### 내용 요약

 - new와 delete는 malloc과 free를 래핑한 함수이다.
 - new와 delete는 malloc과 free와 달리 생성자, 소멸자를 호출한다.
 - new는 동적배열을 할당할 경우 delete[]에서 배열 크기만큼 소멸자를 호출할 수 있도록 배열 크기를 동적메모리 앞 부분에 기록해둔다.
 - delete[]는 동적메모리 앞 부분을 확인하여 동적배열 크기를 확인하고, 그 크기만큼 소멸자를 호출한 뒤 동적메모리를 해제한다.
 - malloc과 delete를 섞어서 사용할 경우 생성자, 소멸자 호출 부분 뿐만 아니라 동적 메모리할당 크기가 차이나기 때문에 메모리해제 단계에서 Crash가 발생할 수 있다.