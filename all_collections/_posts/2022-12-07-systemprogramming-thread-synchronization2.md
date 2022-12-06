---
layout: post 
title: "[SystemProgramming] 스레드 동기화 기법 2"
tags: 
categories:
  - SystemProgramming
---

## Intro
유저모드 동기화 객체와 커널모드 동기화 객체의 차이, <br/> 그리고 유저모드 동기화 객체들의 내부 동작방식 대해 알아보자

<br/>

## 1. 유저모드 동기화 객체와 커널모드 동기화 객체의 차이

 - 유저모드 동기화 객체에는 CRITICIAL_SECTION 과 SRWLOCK 이 있고, 커널모드 동기화 객체에는 Mutex 와 Semaphore 등이 있다. Mutex 와 Semaphore 의 차이는 임계 구역에 진입할 수 있는 키의 개수 차이이다. (Mutex 가 허용하는 키의 개수는 하나 Semaphore 는 여러 개)
 - 유저모드 동기화 객체와 커널모드 동기화 객체의 차이는 동기화 객체에 대한 관리를 유저모드에서 하는지 커널모드에서 하는지의 차이에서 발생한다.

 - 유저모드 동기화 객체는 다른 스레드가 키를 점유중인지 여부를 유저모드 단에서 확인이 가능하기 때문에 점유중이지 않은 경우엔 커널모드로의 전환을 전혀 발생시키지 않고 키를 점유할 수 있다. 다른 스레드가 키를 점유중인 경우엔 block 상태에 돌입하는 과정과 키를 점유했던 다른 스레드가 이를 깨워주는 과정에서 커널모드로의 전환이 발생한다.
 - 커널모드 동기화 객체는 다른 스레드가 키를 점유중인지 확인하는 과정조차 커널모드로의 전환이 필요하다. 따라서 유저모드 동기화 객체보다 속도가 느리며, 대신 동기화 객체의 관리 자체를 커널에서 하기 때문에 유저모드 동기화 객체와 달리 프로세스와 프로세스 간의 동기화가 가능하다.

 - 위 설명대로 커널모드 동기화 객체를 사용하면 프로세스간 동기화가 가능하다는 장점이 있지만 사실 이는 딱히 필요가 없는 장점이다. <br/> 스레드가 아닌 프로세스를 분리했다는 것은 모듈의 물리적인 분리를 고려했다는 것이기 때문에 커널단에서 모듈간 동기화를 맞출 이유가 전혀 없기 때문이다.
 - 따라서 동기화 객체를 선택할 때에는 속도가 빠른 유저모드 동기화 객체를 선택하는 것이 합리적이다. 유저모드 동기화 객체 중 CRITICIAL_SECTION 과 SRWLOCK 어느 것을 선택해야 좋을지는 아래에서 내부 동작방식에 대해 알아보고 판단하도록 하자.

<br/>

## 2. 유저모드 동기화 객체들의 내부 동작방식

### 2.1 CRITICIAL_SECTION

 - 서로 다른 스레드가 크리티컬 섹션 객체를 사용하여 전역변수를 대상으로 ++연산하는 상황을 어셈블리어를 통해 분석해보도록 하자

![image](https://user-images.githubusercontent.com/51254582/205906865-021d1de5-7681-48e3-8d1b-0488edb50d84.png)

 - EnterCriticalSection 함수에서 우선적으로 하는 일은 fs 레지스터에 있는 TEB(Thread Environment Block) 정보를 가져오고, Interlocked 함수를 수행하여 이미 진입한 스레드가 있는지 확인하는 것이다. <br/> 진입한 스레드가 없을 경우 커널모드로 전환하지 않고 바로 키를 획득하게 된다. 키를 획득한 후엔 CRITICIAL_SECTION 객체의 OwningThread 멤버변수에 TEB에 있던 threadID 를 저장하고 RecursionCount 멤버변수를 1로 초기화한뒤 return 한다.

![image](https://user-images.githubusercontent.com/51254582/205915386-33297a16-b6a3-404f-a298-538295e801a7.png)

 - 위 과정에서 Interlocked 함수를 수행하였을 때 이미 진입한 스레드가 있었다면 CRITICIAL_SECTION 객체의 OwningThread 멤버와 현재 스레드의 ID를 비교한다. 비교 결과가 같으면 재귀 진입으로 판단하여 RecursionCount 멤버변수를 1 증가시키고 return 하고, 비교 결과가 다르면 RtlpEnterCriticalSection 라는 함수를 call 한다.

![image](https://user-images.githubusercontent.com/51254582/205917989-67d13313-fe3d-4c78-9d21-1ac91a230979.png)

 - 위 과정에서 스레드ID 비교 결과가 달라 재귀 진입이 아니라고 판단되어 NtWaitForAlertByThread 가 호출되면 래핑되어있는 함수들을 호출하며 들어가서 마지막에 NtWaitForAlertByThread 라는 함수를 call 하게 된다. <br/> 이는 
[WaitOnAddress](https://learn.microsoft.com/ko-kr/windows/win32/api/synchapi/nf-synchapi-waitonaddress) API 로써 Block 상태로 돌입하여 다른 스레드가 WakeByAddressSingle 함수를 호출하여 깨워줄때까지 대기하는 함수이다. <br/> 결국 CRITICIAL_SECTION 객체는 이때서야 비로서 커널모드로 전환이 발생한다.

![image](https://user-images.githubusercontent.com/51254582/205922271-42ecf5fc-2350-45f6-ad7e-22044f7854d5.png)

<br/>

### 2.2 SRWLOCK

 - 마찬가지로 서로 다른 스레드가 SRWLOCK 객체를 사용하여 전역변수를 대상으로 ++연산하는 상황을 어셈블리어를 통해 분석해보도록 하자

![image](https://user-images.githubusercontent.com/51254582/205931177-9f09e7c3-ec09-48c8-ab6a-99b0ca77ab77.png)

 - AcquireSRWLockExclusive 함수는 같은 스레드라도 재귀 진입을 허용하지 않기 때문에 CRITICIAL_SECTION 과 달리 TEB 정보를 가져오는 과정을 생략하고 바로 Interlocked 함수를 수행하여 이미 진입한 스레드가 있는지 확인한다.
 - 진입한 스레드가 없을 경우 키를 획득한 것으로 별다른 코드를 수행하지 않고 바로 return 한다.
 - 진입한 스레드가 있을 경우 CRITICIAL_SECTION 과 마찬가지로 [WaitOnAddress](https://learn.microsoft.com/ko-kr/windows/win32/api/synchapi/nf-synchapi-waitonaddress) API 인 NtWaitForAlertByThread 함수를 call 하여 커널모드로 전환된 후 Block 상태로 돌입하고 다른 스레드가 WakeByAddressSingle 함수를 호출하여 깨워줄때까지 대기하게 된다.

![image](https://user-images.githubusercontent.com/51254582/205927764-dd967224-eba8-4a67-8849-8acbb7076267.png)

<br/>

## 3 동기화 객체 정리

 - 동기화 객체에는 유저모드 동기화 객체와 커널모드 동기화 객체가 있다.
 - 커널모드 동기화 객체는 유저모드 동기화 객체보다 느리고 유용한 장점이 없기 때문에 잘 사용하지 않는다.
 - 유저모드 동기화 객체에는 CRITICIAL_SECTION 과 SRWLOCK 이 있다.
 - CRITICIAL_SECTION 은 같은 스레드에게 재귀 진입을 허용하기 때문에 스레드ID 와 재귀카운트를 저장하는 코드가 존재한다. <br/> SRWLOCK 은 같은 스레드더라도 재귀 진입을 허용하지 않기 때문에 스레드 정보를 따로 저장하는 코드가 없다.
 - 따라서 같은 스레드의 재귀 진입이 필요할 경우엔 CRITICIAL_SECTION 을 사용하고, 재귀 진입이 필요하지 않을 경우 상대적으로 더 빠른 SRWLOCK 를 사용하는 것이 합리적이다.