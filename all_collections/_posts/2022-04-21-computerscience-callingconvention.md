---
layout: post
title: "[ComputerScience] 함수호출 규약!(어셈블리 분석 포함)"
tags: 
categories:
  - ComputerScience
---

## Intro
C언어 프로그램의 함수호출 규약에 대해 알아보자

<br/>

## 1. 함수호출 규약
- 함수 호출 규약(Calling Convention)이란 함수를 호출하는 방식에 대한 약속이다.
- 함수 호출 규약은 인자 전달 방법, 인자 전달 순서, Stack Frame을 정리하는 방법에 따라 그 종류를 구분한다.
![image](https://user-images.githubusercontent.com/51254582/164414528-320d6093-1810-4829-8147-ec83bbadcc17.png)

<br/>

### 1.1 32Bit 시스템 함수호출 규약

||`인자 전달 순서`|`인자 전달 방법`|`스택 프레임 정리`|`가변 인자 사용여부`|
|---|----|---|
|1|`cdecl`|스택 메모리 사용|Caller(호출자)가 정리|가능
|2|`stdcall`|스택 메모리 사용|Callee(피호출자)가 정리|불가능
|3|`fastcall`|레지스터 및 스택메모리 사용|스택 메모리 사용시 Callee(피호출자)가 정리|불가능

<br/>

1) cdecl <br/>
- 스택 메모리 사용
- Caller(호출자)가 스택 메모리 정리
![image](https://user-images.githubusercontent.com/51254582/164417612-3c240fe2-8e11-44e0-869a-9e55a8c719ac.png)

<br/>

2) stdcall <br/>
- 스택 메모리 사용
- Callee(피호출자)가 스택 메모리 정리
![image](https://user-images.githubusercontent.com/51254582/164418343-3557e147-d347-41c5-90df-bf26f69f6387.png)

<br/>

3) fastcall <br/>
- 레지스터 사용 (레지스터 사용으로 인해 메모리 정리 과정 생략)
![image](https://user-images.githubusercontent.com/51254582/164419526-5faf652b-39b0-4ed5-ac75-c7c1b0ed3607.png)
