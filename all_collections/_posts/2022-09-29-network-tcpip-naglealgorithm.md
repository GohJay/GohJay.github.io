---
layout: post
title: "[Network] 네이글 알고리즘이란?"
tags: 
categories:
  - Network
---

## Intro
네이글 알고리즘에(Nagle Algorithm) 대해 알아보자

<br>

## 1. 네이글 알고리즘

![image](https://user-images.githubusercontent.com/51254582/200104934-44047eb4-3ae0-41d3-96f0-5a12f892a604.png)

 - 네이글 알고리즘은 현재 TCP에서 사용중인 Sliding window 방식에서 버퍼링 기능이 추가된 알고리즘으로 '가능하면 조금씩 여러번 보내지 말고, 한번에 많이 보내라'를 원칙으로 만들어진 알고리즘이다. <br> (Sliding window 방식에 대한 설명은 [TCP의 제어 기능 3가지](https://gohjay.github.io/posts/network-tcpip-control/) 포스트 참고)
 - 네트워크 송수신을 할 때 L7 애플리케이션에서 4 Byte만 Send 요청을 하였다고 하더라도 L4에서 TCP헤더가 20 Byte 붙고, L3에서 IP헤더가 20 Byte 붙게되므로 실제 패킷 크기는 44 Byte가 된다. <br> 따라서 네이글 알고리즘을 사용하여 TCP에서 L7에서의 Send 단위로 실제 전송을 수행하지 않고 따로 버퍼링을 해두었다가 데이터를 모아서 전송하게 되면 네트워크 트래픽을 유의미하게 감소 시킬 수 있다.

### 1.1 네이글 알고리즘 동작 조건

 - 첫째, 상대방으로부터 응답이(ACK) 오면 버퍼링 해둔 데이터를 전송한다.
 - 둘째, 버퍼링 해둔 데이터가 MSS(Maximum Segment Size) 크기를 만족하면 버퍼링 해둔 데이터를 전송한다.

### 1.2 네이글 알고리즘 장단점

 - 장점: 버퍼링을 함으로써 네트워크 트래픽이 감소한다.
 - 단점: 버퍼링을 함으로써 네트워크 응답성이 감소한다. <br> (빠른 응답성이 요구되는 게임이라면 네이글 알고리즘 작동 Off를 고려해 볼 수 있다.)

<br>

## 2. C++ 네이글 알고리즘 옵션 설정 방법

```C++
int option = TRUE;               	//네이글 알고리즘 on/off
setsockopt(socket,             		//해당 소켓
           IPPROTO_TCP,          	//소켓의 레벨
           TCP_NODELAY,          	//설정 옵션
           (const char*)&option, 	//옵션 포인터
           sizeof(option));      	//옵션 크기
```

 - 기본적으로 소켓 옵션은 네이글 알고리즘을 사용하도록 설정되어 있다. <br> 응답성 향상을 위해 네이글 알고리즘을 동작시키지 않고 싶다면 해당 함수를 사용하여 수동으로 꺼주어야 한다.
 - setsockopt 함수를 통해 사용 소켓 옵션을 TCP_NODELAY로 설정하면, 해당 소켓에 대해 네이글 알고리즘 작동을 사용하지 않을 수 있다.
