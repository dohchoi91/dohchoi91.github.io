---
title: "12.직렬화"
subtitle: "아이템85 ~ 아이템90"
permalink: /notes/effective-java/ch12
author_profile: true
date: 2021-04-02 00:20
categories:
  - 이펙티브 자바
tags:
  - java
last_modified_at: 2021-04-02 00:20
---

객체 직렬화란 자바가 객체를 바이트 스트림으로 인코딩하고(직렬화) 그 바이트 스트림으로부터 다시 객체를 재구성하는(역직렬화) 매커니즘이다.

## 아이템85 : 자바 직렬화의 대안을 찾으라

> 직렬화는 위험하니 피해야 한다. 시스템을 밑바닥부터 설계한다면 JSON이나 프로토콜 버퍼 같은 대안을 사용하자. 신뢰할 수 없는 데이터는 역직렬화하지 말자.

- 직렬화의 근본적인 문제는 공격 범위가 너무 넓고 지속적으로 넓어져 방어하기가 어렵다는 점이다.
- 역직렬화 과정에서 호출되어 잠재적으로 위험한 동작을 수행하는 메서드들을 가젯(gadget)이라 한다.
- 역직렬화에 시간이 오래걸리는 짧은 스트림을 역직렬화 하는 것만으로도 서비스 거부 공격에 쉽게 노출될 수 있다.
- 직렬화 위험을 회피하는 가장 좋은 방법은 아무것도 역직렬화하지 않는 것이다.
- 레거시 시스템 때문에 자바 직렬화를 완전히 배제할 수 없을 때의 차선책은 신뢰할 수 없는 데이터는 절대 역직렬화하지 않는 것이다.

## 아이템86 : Serializable을 구현할지는 신중히 결정하라

> Serializable은 구현한다고 선언하기는 아주 쉽지만, 그것은 눈속임일 뿐이다. 한 클래스의 여러 버전이 상호작용할 일이 없고 서버가 신뢰 할 수 없는 데이터에 노출된 가능성이 없는 등, 보호된 환경에서만 쓰일 클래스가 아니라면 Serializable 구현은 아주 신중하게 이뤄져야 한다.

클래스의 인스턴스를 직렬화 할 수 있게 하려면 클래스 선언에 `Serializable` 을 구현하면 된다.

### Serializable 구현 시 문제점

- `Serializable` 을 구현하면 릴리스한 뒤에는 수정하기가 어렵다.
- 버그와 보안 구멍이 생길 위험이 높아진다는 점이다.
- 해당 클래스의 신버전을 릴리스할 때 테스트할 것이 늘어나는 것이다.
- 상속용으로 설계된 클래스는 대부분 `Serializable`을 구현하며 안되며, 인터페이스도 대부분 `Serializable`을 확장 해서는 안된다.
- 내부 클래스는 직렬화를 구현하지 말아야 한다.

## 아이템87 : 커스텀 직렬화 형태를 고려해보라

> 클래스를 직렬화하기로 했다면 어떤 직렬화 형태를 사용할지 심사숙고하기 바란다. 자바의 기본 직렬화 형태는 객체를 직렬화한 결과가 해당 객체의 논리적 표현에 부합할 때만 사용하고, 그렇지 않으면 객체를 적절히 설명하는 커스텀 직렬화 형태를 고안하라. 공개 메서드는 향후 릴리스에서 제거 할 수없듯이, 직렬화 형태에 포함된 필도도 마음대로 제거할 수 없다. 직렬화 호환성을 유지하기 위해 영원히 지원해야 하는 것이다.

### 객체의 물리적 표현과 논리적 표현의 차이가 클 때 기본직렬화 형태 사용시 발생되는 문제점

1. 공개 API가 현재의 내부 표현 방식에 영구히 묶인다.
2. 너무 많은 공간을 차지 할 수 있다.
3. 시간이 너무 많이 걸릴수 있다.
4. 스택 오버플로를 일으킬 수 있다.

## 아이템88 : readObject 메서드는 방어적을으로 작성하라

> readObject 메서드를 작성할 때는 언제나 public 생성자를 작성하는 자세로 임해야한다. readObject는 어떤 바이트 스트림이 넘어오더라도 유효한 인스턴스를 만들어내야 한다. 바이트 스트림이 진짜 직렬화된 인스턴스라고 가정해서는 안 된다.

### readObjejct 메서드 작성 지침

- `private`이어야 하는 객체 참조 필드는 각 필드가 가리키는 객체를 방어적으로 복사하라. 불변 클래스 내의 가변 요소가 여기 속한다.
- 모든 불변식을 검사하여 어긋나는 게 발견되면 `InvalidObjectException`을 던진다. 방어적 복사다음에는 반드시 불변식 검사가 뒤따라야 한다.
- 역직렬화 후 객체 그래프 전체의 유효성을 검사해야 한다면 `ObjectInputValidation`인터페이스를 사용하라
- 직접적이든 간접적이든, 재정의할 수 있는 메서드는 호출하지 말자

## 아이템89 : 인스턴스 수를 통제해야한다면 readResolve보다는 열거 타입을 사용하라

> 불변식을 지키기 위해 인스턴스를 통제해야 한다면 가능한 한 열거 타입을 사용하자. 여의치 않은 상황에서 직렬화와 인스턴스 통제가 모두 필요하다면 readResolve 메서드를 작성해 넣어야 하고, 그 클래스에서 모든 참조타입 인스턴스 필드를 transiendt로 선언해야한다.

## 아이템90 : 직렬화된 인스턴스 대신 직렬화 프록시 사용을 검토하라

> 제 3자가 확장할 수 없는 클래스라면 가능한 한 직렬화 프록시 패턴을 사용하자. 이 패턴이 아마도 중요한 불변식을 안정적으로 직렬화해주는 가장 쉬운 방법일 것이다.