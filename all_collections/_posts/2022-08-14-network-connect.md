---
layout: post
title: "[Network] 네트워크에서의 연결이란?"
tags: 
categories:
  - Network
---

## Intro
네트워크에서의 연결이라는 개념에 대해 알아보자

<br>

## 1. 네트워크에서의 연결

 - 이전 포스트에서 네트워크 계층과 TCP/UDP 프로토콜에 대해 다루었다.
 - TCP 통신에서 우리는 Listen 중인 서버에 클라이언트가 Connect를 하여 연결을 성립한 후 서로 통신을 주고 받는다.
 - 그렇다면 물리적으로 연결되어있지 않은 장비끼리 어떻게 연결이 성립할 수 있는 것이며 이는 몇 계층에서 이루어지는 것일까?
 - .
 - .
 - .
 - 결론부터 말하자면, 네트워크에서의 연결이란 서버와 클라이언트의 프로토콜 스택에서(L4) 서로 연결되었다고 가정하는 것이다. 이에 대한 Flow는 다음과 같다.
 - 먼저 연결을 시도하는 클라이언트의 프로토콜 스택에서 "이곳의 IP 주소는 xxx.xxx.xxx.xxx이고 포트 번호는 yyyy입니다. 데이터 송수신을 하고싶은데 어떠세요?" 라고 서버측에 전달한다.
 - 이로써 서버측 프로토콜 스택도 클라이언트의 정보를 가질 수 있게 되었다. 서버는 이에 대한 응답으로 "네 요청을 수락합니다." 라고 클라이언트측에 전달한다.
 - 마지막으로 클라이언트가 "확인했습니다." 라고 서버측에 전달하면서 양쪽 모두 데이터를 전송할 준비가 되었다는 것을 보장한다.
 - 이러한 연결을 위한 통신 과정을 3way-handshake 라고 한다.
 - 반대로 연결 종료를 하기 위한 통신 과정은 4way-handshake 이다.
 - handshake 과정에 대한 자세한 설명은 아래와 같다.

<br>

### 1.1 TCP의 3way-handshake

![image](https://user-images.githubusercontent.com/51254582/184508729-05d69dbd-81da-40f5-8d52-87bfd0032187.png)

 - [Step1] 클라이언트는 서버에 접속을 요청하는 SYN 패킷을 보낸다. <br> 이때 클라이언트는 SYN을 보내고 SYN/ACK 응답을 기다리는 SYN_SENT 상태가 된다.
 - [Step2] 서버는 SYN 요청을 받고 클라이언트에게 요청을 수락한다는 ACK와 SYN Flag가 설정된 패킷을 전송하고 클라이언트가 다시 ACK로 응답하기를 기다린다. 이때 서버의 상태는 SYN_RECEIVED 상태이다.
 - [Step3] 클라이언트는 서버에게 ACK를 보내고 이후부터는 서로 연결되었다고 가정하고 L7에서 보낸 메시지를 주고받는다. 연결되었다는 의미의 상태가 ESTABLISHED 이다.

<br>

### 1.2 TCP의 4way-handshake

![image](https://user-images.githubusercontent.com/51254582/184509033-f1d06574-1744-4554-a535-257741b10ec1.png)

 - 연결의 경우 항시 클라이언트가 서버에 먼저 요청을 해야하지만 연결 종료는 어느쪽이든 먼저 진행할 수 있다.
 - 본문에서는 편의상 클라이언트가 먼저 연결 종료를 진행하였다고 가정하고 설명하도록 하겠다. <br>

 - [Step1] 클라이언트가 더 이상 메시지를 보낼 것이 없다는 의미의 FIN 플래그가 담긴 패킷을 전송한다.
 - [Step2] 서버는 일단 ACK로 응답을 보내고 자신의 L7 애플리케이션에게 연결 종료에 대한 정보를 통지한다. <br> 이때 서버는 L7 애플리케이션이 통신 종료 시도할 때까지 기다려야 하므로 TIME_WAIT 상태로 돌입한다.
 - [Step3] 서버측 L7 애플리케이션에서 해당 소켓과 TCP자원을 반환하려는 함수를 호출하면 클라이언트에게 FIN 플래그가 담긴 패킷을 전송한다. <br> 이때 상태는 클라이언트측의 마지막 응답을 기다린다고 하여 LAST_ACK 이다.
 - [Step4] 클라이언트는 확인하였다는 ACK를 전송하고 CLOSE_WAIT 상태로 돌입한다. <br>

 - CLOSE_WAIT 상태는 서버에서 FIN을 전송하기 전에 전송한 메시지 패킷이 Routing 지연이나 패킷 유실로 인한 재전송 등으로 인해 FIN 패킷보다 늦게 도착하는 상황을 우려한 것이다. <br> ( 사실은 우려할 필요가 전혀 없다는 함정이 있다.. )

<br>

#### [TIP] TCP 연결 종료시 TIME_WAIT 상태가 먼저 연결을 종료한 쪽에만 남는 이유

 - 먼저, TIME_WAIT 상태가 되었을 경우의 단점을 생각해보아야 한다.
 - TCP 계층은 항시 연결 정보를 기억하고 있어야 하고, 연결은 1:1 이므로 연결된 개체마다 송수신 버퍼를 생성해야 하므로 연결이 일어날 때마다 리소스 자원이 소모된다.
 - TIME_WIAT 상태로 돌입하여 연결 정보를 유지하게 되면 그만큼 사용한 리소스에 대한 반환이 늦어지게 되므로 이는 좋지 못하다.
 - TCP 에서 연결이란 클라이언트와 서버가 서로의 접속정보(IP, Port)를 기억하고 연결되었다고 가정하는 것이라고 하였다.
 - 그렇다면 한 쪽의 Port가 TIME_WAIT 상태로 돌입하게 된다면 해당 IP, Port 와는 TIME_WAIT 상태가 끝날때 까지 다시 연결될 수 있을까? X
 - 결론적으로 한 쪽에만 TIME_WAIT 상태를 두더라도 TIME_WAIT 상태가 유지되는 동안 서로 다시 연결될 수 있는 방법이 없으므로, 양 쪽에서 TIME_WAIT 상태를 유지할 이유가 없다. <br> 따라서 TCP는 리소스 낭비를 최소화 하기 위해 먼저 연결을 종료한 쪽에만 TIME_WAIT 상태를 남기고 있다.