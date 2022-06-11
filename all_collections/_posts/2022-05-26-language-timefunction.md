---
layout: post
title: "[C/C++] 시간 관련 함수!"
tags: 
categories:
  - Language
---

## Intro
컴퓨터에서 시간을 측정하는 원리와 시간과 관련된 함수들에 대해 알아보자

<br/>

## 1. 컴퓨터에서의 시간

 - 컴퓨터는 사람이 정한 1초라는 단위를 어떻게 알고 있는걸까?
 - 컴퓨터는 두 가지 방법으로 시간을 측정할 수 있다.
 - 첫째, 하드웨어 타이머를 통해 측정할 수 있다. <br/> 운영체제는 하드웨어 타이머로부터 주기적으로 타이머 인터럽트 신호(IRQ 1번)를 받아 시간을 관리한다.
 - 둘째, CPU 클럭을 통해 측정할 수 있다. <br/> 클럭은 1초 동안 파장이 한 번 움직이는 시간을 의미한다. rdtsc(Read Time Stamp Counter)라는 어셈블리 명령어를 통해 CPU 클럭 수를 측정할 수 있다.

<br/>

### 2. 시간 관련 함수

 - 시간 관련 함수에 대해 비교 분석해보도록 하자
 - 하드웨어 타이머를 통해 측정하는 함수 <br/> GetTickCount(), timeGetTime()
 - CPU 클럭을 통해 측정하는 함수 <br/> QueryPerformanceCounter(), clock()

<br/>

#### 2.1 하드웨어 타이머를 통해 측정하는 함수

##### 1) GetTickCount()

 - 시스템이 시작된 시간으로부터의 측정 값. 64frame(default 해상도) 단위
 - 일반적으로 윈도우 환경에서 ms 단위의 시간을 원할 경우 사용한다.
 - 주의사항으로는 반환 값이 DWORD(unsigned long)이기 때문에 시스템이 시작된 시간이 49.7일을 초과할 시 값이 초기화 된다. 이 문제를 해결하기 위해서는 GetTickCount64() 함수를 사용하여야 한다.
 - 아래 화면과 같이 시스템이 시작된 시간이 15.6ms 단위로(1000ms % 64frame) 측정 되는 것을 확인할 수 있다. 
 ![image](https://user-images.githubusercontent.com/51254582/170455430-011f32d1-fa3b-4c03-aca0-22e7d1dae3bb.png)

##### 2) timeGetTime()

 - 시스템이 시작된 시간으로부터의 측정 값. 1000frame 단위까지 표현 가능
 - timeGetTime()은 보다 높은 해상도 단위를 표현할 수 있어 멀티미디어 타이머라고도 불린다.
 - 시스템 타이머 해상도를 변경하기 위한 방법으로는 timeBeginPeriod(n)가 있다. timeBeginPeriod(n)은 SystemTimerInterval을 n으로 변경한다. 이를 원복하려면 timeEndPeriod(n)를 호출하면 된다.
 - 아래 화면과 같이 시스템이 시작된 시간이 1ms 단위로(1000frame) 측정 되는 것을 확인할 수 있다.
 ![image](https://user-images.githubusercontent.com/51254582/170458483-bd8eb81e-a435-4a19-8d78-b676348885b2.png)
 
 <br/>
 
#### 2.2 CPU 클럭을 통해 측정하는 함수

##### 1) QueryPerformanceCounter()
 
 - CPU 클럭을 측정한 값. 100 nanosecond 단위까지 표현 가능
 - rdtsc 어셈블리 명령어를 래핑한 Windows API 이다.
 - QueryPerformanceFrequency 함수로 주파수를 검색해서 100 nanosecond가 아닌 second 단위로 변환하여 사용할 수 있다.
 - 아래 화면과 같이 CPU 클럭 수가 측정 되는 것을 확인할 수 있다.
 ![image](https://user-images.githubusercontent.com/51254582/172424277-7dcad0ec-0510-4999-af43-0aa7ad917709.png)

##### 2) clock()

 - 프로세스가 시작된 시간으로부터의 측정 값. 1000frame 단위까지 표현 가능
 - QueryPerformanceCounter 함수를 래핑한 C++ 기반의 함수이다.
 - ctime header에 정의되어있는 상수 CLOCKS_PER_SEC와 같이 사용하면 sec(초) 단위로 출력할 수 있다.
 - 아래 화면과 같이 프로그램이 시작된 시간이 1ms 단위로(1000frame) 측정 되는 것을 확인할 수 있다.
 ![image](https://user-images.githubusercontent.com/51254582/170466394-1da0a3d2-4d4a-4151-9143-430755726013.png)
 