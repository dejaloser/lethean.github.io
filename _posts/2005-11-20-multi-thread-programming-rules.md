---
layout: post
title: "멀티쓰레드 프로그래밍 규칙"
tags: [ Coding,  GLib ]
---

멀티 쓰레드 방식 프로그래밍은 강력한 효율을 제공하지만 그만큼 프로그래머를 힘들게 하기도 한다. 그렇다면, 멀티 쓰레드 방식을 대체할 수 있는 방법은 무엇이 있을까? 물론 많은 사람들이 이 주제에 대하여 책도 쓰고 글도 남겼지만 여기는 내 블로그니까, 가능한 내 경험에 비추어 생각해본다.

### 뮤텍스(mutex)를 사용해야 하는 구조로 설계하지 말기

뮤텍스를 이용하는 근본적인 이유는 하나의 리소스에 대해 동시에 접근할 경우 문제가 발생할 수 있을 경우 이에 대한 상호 배제(Mutual Exclusion)를 하기 위함이다. 하지만 하나의 리소스 또는 기능에 대한 처리를 전담하는 쓰레드를 만들고, 원하는 작업을 이 쓰레드(또는 프로세스)에게 메시지로 전달하여 처리하는 방식을 이용하면 뮤텍스를 사용해야 하는 경우는 사라지게 된다. 즉, 메시지 처리 방식(message-driven)을 이용하는 것이다. 물론 이 방식은 비동기적인 메시지일 경우 별로 문제가 없지만, 동기화가 필요한 경우 복잡해진다는 단점이 있다. 또한 성능 문제도 무시할 수 없다.

### 폴링(polling) 방식을 적극적으로 이용하고 쓰레드로 분리하지 않기

멀 티쓰레드 프로그래밍시 발생하는 대부분의 문제는 한번 쓰레드 방식을 이용하게 되면 이후 추가되는 모든 구현에 적극적으로 쓰레드를 도입하게 되는 습관에서 시작한다. 특히 IO(비디오,디스크,네트워크) 관련 처리에 대한 비동기 처리를 폴링 방식으로 프로그래밍하면 느려지고 복잡하기 때문에 쓰레드로 분리해서 쉽게 해결하지만, 바로 이때부터가 재앙의 시작이다. 경험상으로 보면 가장 큰 기능별로 쓰레드를 분리하는 것이 가장 바람직하며, 정말로 성능에 별로 지장을 주지 않는다면 조금 수고가 들더라도 비동기 방식으로 제작하여 한 쓰레드에서 수행되도록 하는 것이 데드락(deadlock)과 같은 저주를 피하면서 뇌세포에게 더 많은 휴식을 줄 수 있는 방법일 것이다.

### 이미 잘설계된 라이브러리를 이용하기

[GLib](http://www.gtk.org/) 같은 라이브러리를 살펴보면 위의 원칙대로 프로그래밍하기 위한 API가 너무나 잘 지원되고 있다. 욕심이 나지 않을 수가 없다. 다만, 임베디드 시스템에도 이용할 수 있도록 다른 라이브러리에 대한 의존성이 적고, WinCE 등과 같은 플랫폼도 문제없이 지원한다면 현재 진행중인 모든 프로젝트의 기본 라이브러리로 채택하지 않을 이유가 없을텐데 말이다.

물론 다른 규칙도 많겠지만, 오늘은 일단 여기까지만... :)