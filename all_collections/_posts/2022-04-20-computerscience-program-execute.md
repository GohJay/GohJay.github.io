---
layout: post
title: "[ComputerScience] 프로그램의 실행과정!"
tags: 
categories:
  - ComputerScience
---

## Intro
프로그램의 실행과정에 대해 알아보자

<br/>

## 1. 프로그램 컴파일 과정

![image](https://user-images.githubusercontent.com/51254582/164413825-e0f7d386-dc81-483b-ae91-7a4a8d0242ef.png)

||`과정`|`설명`|
|---|----|---|
|1|`전처리기`|전처리기는 '#include', '#define'과 같이 '#'으로 시작하는 지시자의 지시에 따라서 소스코드를 적절히 변경하는 작업을 한다.|
|2|`컴파일러`|전처리기에 의해 변경된 소스코드는 여전히 고수준 언어로 구성되어 있다. 이 소스코드는 컴파일러에 의해서 저수준 언어(어셈블리 코드)로 번역된다.|
|3|`어셈블러`|바이너리코드는 1과 0으로만 구성되는 코드이다. 컴파일러에 의해 번역된 어셈블리 코드는 컴퓨터에 의해 실행되기 앞서서 바이너리 코드로 번역 되어야 한다. 컴퓨터는 1과 0만 이해하기 때문이다. 이처럼 CPU가 이해할 수 있는 바이너리 코드로 바꾸어 주는 프로그램이 어셈블러이다.|
|4|`링커`|링커는 프로그램 내에서 참조하는 함수나 라이브러리들을 하나로 묶는(혹은 연결시켜 주는)작업을 한다고 설명할 수 있다. 이 과정이 끝나면 실행파일이 생성된다. 실행파일은 바이너리 코드로 구성된다.|

<br/>

## 2. 프로그램 실행 과정

![image](https://user-images.githubusercontent.com/51254582/164413313-a106c197-08cf-4eab-bc09-c906236fefc9.png)

 - 실행파일이 메모리에 올라가고 난 다음 CPU에 의해 실행되기 시작한다.
 - 메모리에 올라간 명령어들은 CPU에 의해서 순차적으로 실행된다.
 - 명령어 A, 명령어 B, 명령어 C 순서대로 이다. 이명령어 들은 메모리에서 실행되는 것이 아니라 CPU내부로 하나씩 이동한 다음 실행시킨다.

<br/>

### 2.1 명령어 실행 과정

||과정|설명|
|---|----|---|
|1|`Fetch`|메모리상에 존재하는 명령어를 CPU로 가져오는 작업이다.|
|2|`Decode`|가져온 명령어를 CPU가 해석하는 단계이다. 즉, 무슨일을 하라는 명령어인지 분석하는 단계이다.|
|3|`Execution`|해석된 명령어의 명령대로 CPU가 실행하는 단계이다.|

<br/>

### 2.2 하드웨어 구성의 재접근

![image](https://user-images.githubusercontent.com/51254582/164413451-9e048e11-989a-4cac-99f0-082c2b229d5c.png)

 - 메인메모리에서 CPU로 가져온 명령어는 레지스터에 저장된다. 레지스터 중에서도 IR(Instruction Register)이라 불리는 레지스터에 저장된다.
 - 컨트롤 유닛(Control Unit)은 CPU를 제어하는하는 부품이다. 컨트롤 유닛은 명령어를 해석하고 레지스터와 ALU 사이의 명령 흐름을 제어한다.
 - 명령어의 내용이 산술 및 논리연산이라면 산술 및 논리 연산을 하는 Execution의 주체는 ALU(Arithmetic Logic Unit)이다.

<br/>

### 2.3 레지스터

 - 컴퓨터의 프로세서 내에서 자료를 보관하는 아주 빠른 기억 장소이다.
 - 일반적으로 현재 계산을 수행중인 값을 저장하는 데 사용된다.
 - 대부분의 현대 프로세서는 메인 메모리에서 레지스터로 데이터를 옮겨와 데이터를 처리한 후 그 내용을 다시 레지스터에서 메인 메모리로 저장하는 로드-스토어 설계를 사용하고 있다.
 - [TIP] x86 아키텍처란? 어셈블리어와 CPU 레지스터의 명령어 Set이 동일한 것
