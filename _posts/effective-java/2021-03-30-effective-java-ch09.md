---
title: "09.일반적인 프로그래밍 원칙"
subtitle: "아이템57 ~ 아이템68"
permalink: /notes/effective-java/ch09
author_profile: true
date: 2021-03-30
categories:
  - 이펙티브 자바
tags:
  - java
last_modified_at: 2021-03-30
---

자버 언어의 핵심 요소에 집중한다. 지역변수, 제어구조, 라이브러리, 데이터 타입, 리플렉션, 네이티브 메서드를 다룬다. 마지막으로 최적화와 명명 규칙을 논한다.

## 아이템57 : 지역변수의 범위를 최소화하라

> 지역변수의 유효 범위를 최소로 줄이면 코드 가독성과 유지보수성이 높아지고 오류 가능성이 낮아진다.

### 지역변수의 범위 줄이기

- 가장 처음 쓰일 때 선언하기
- 모든 지역변수는 선언과 동시에 초기화해야 한다
- 컬렉션 순회 할 때 권장하는 관용구

    ```java
    for (Element e : c) {
    	...
    }
    ```

- 반복자가 필요할 때의 관용구

    ```java
    for (Iterator<Element> i = c.iterator(); i.hasNext();) {
    	Element e = i.next();
    }
    ```

- 메서드를 작게 유지하고 한가지 기능에만 집중한다.

### for문의 while문 보다 나은 이유

- 복사-붙여넣기의 오류를 유효범위로 인하여 컴파일 타임에 잡아준다.
- 가독성

## 아이템58 : 전통적인 for문 보다는 for-each문을 사용하라

> 전통적인 for문과 비교했을 때 for-each문은 명료하고, 유연하고, 버그를 예방해준다. 성능 저하도 없다. 가능한 모든 곳에서 for문이 아닌 for-each문을 사용하자.

`for-each`문은 전통적인 `for`문에서 반복자와 인덱스 변수가 생기는 요소에서 변수의 잘못 사용할 가능성을 줄여준다.

### 컬렉션이나 배열의 중첩 반복을 위한 권장 관용구

```java
for(Suit suit : suits)
	for(Rank rank : ranks)
		deck.add(new Card(suit, rank));
```

### for-each문을 사용할 수 없는 상황

- **파괴적인 필터링** : 컬렉션을 순회하면서 선택된 원소를 제거하는 경우
- **변형** : 리스트나 배열을 순회하면서 그 원소의 값 일부 혹은 전체를 교체해야 한다면 리스트의 반복자나 배열의 인덱스를 사용해야 한다.
- **병렬반복** : 여러 컬렉션을 병렬로 순회해야 할 경우

## 아이템59 : 라이브러리를 익히고 사용하라

> 바퀴를 다시 발명하지 말자. 보편적인 부분은 라이브러리 형태로 구현해놓았을 가능성이 크다. 라이브러리 코드는 직접 작성하는 것보다 품질이 좋고 점차 개선될 가능성이 크다.

### 표준 라이브러리 사용 이점

1. 표준 라이브러리를 사용하면 그 코드를 작성한 전문가의 지식과 앞서 사용한 다른 프로그래머들의 경험을 활용 할 수 있다.
2. 핵심적인 일과 크게 관련 없는 문제를 해결하느라 시간을 허비하지 않아도 된다.
3. 노력하지 않아도 성능이 지속해서 개선된다.
4. 기능이 점점 많아진다.
5. 작성한 코드가 다른 개발자들이 더 읽기 좋고, 유지보수하기 좋고, 재활용하기 쉬운 코드가 된다.

***자바 프로그래머라면 적어도 `java.lang`, `java.util`, `java.io`와 하위 패키지들에 익숙해져야 한다.***

## 아이템60 : 정확한 답이 필요하다면 float과 double은 피하라

> 정확한 답이 필요한 계산에는 float이나 double을 피하라. 
소수점 추적은 시스템에 맡기고, 코딩 시 불편함, 성능저하를 신경쓰지 않겠다면 BigDecimal을 사용하라. (비즈니스 계산에서는 아주 편리하다.)
9자리 이하일 경우 int, 9자리에서 18자리일 경우 long, 18자리가 넘어갈 경우 BigDecimal을 사용하라.

### float과 double

- 과학과 공학계산용으로 설계되었다.
- 넓은 범위의 수를 빠르게 정밀한 '근사치'로 계산하도록 세심하게 설계되었다.
- **정확한 결과가 필요할 때 사용해서는 안 된다. 특히 금융 관련 계산과는 맞지 않는다.**

### 금융 계산 적합한 타입

`**BigDecimal`, `int`, `long`**

### `**BigDecimal` 의 단점**

1. 기본 타입보다 쓰기 불편하다.
2. 기본 타입보다 느리다.

## 아이템61 : 박싱된 기본 타입보다는 기본 타입을 사용하라

> 기본 타입과 박싱된 기본 타입 중 하나를 선택해야 한다면 가능하면 기본 타입을 사용하라. 오토박싱이 박싱된 기본 타입을 사용할 때의 번거로움을 줄여주지만, 그 위험까지 없애 주지는 않는다.

***자바의 데이터 타입은 크게 기본 타입과 참조 타입으로 나눌 수 있다.*** 

### 기본 타입과 박싱된 타입의 차이

1. 기본 타입은 값만 가지고 있으나, 박싱된 타입은 값에 더해 식별성(identity)이란 속성을 갖는다.
2. 기본 타입의 값은 언제나 유효하나, 박싱된 기본 타입은 `null` 을 가질 수 있다.
3. 기본타입이 박싱된 기본 타입보다 시간과 메모리 사용면에서 효율적이다. (성능)

### 박싱된 기본 타입 사용시 주의사항

1. 박싱된 기본 타입에 == 연산자를 사용하면 오류가 발생한다.
    - `Comparator.naturalOrder()` 를 사용하자.
    - 박싱된 타입을 기본 타입으로 변환해서 사용하자
2. 기본 타입과 박싱된 기본 타입을 혼용한 연산에서는 박싱된 기본 타입의 박싱이 자동으로 풀린다. (언박싱 과정에서 `NullPointerException` 이 발생 할 수 있다.)

### 박싱된 기본 타입을 사용할 경우

1. 컬렉션의 원소, 키, 값으로 쓰이는 경우
2. 리플렉션을 통해 메서드를 호출 할 때

## 아이템62 : 다른 타입이 적절하다면 문자열 사용을 피하라

> 더 적합한 데이터 타입이 있거나 새로 작성할 수 있다면, 문자열을 쓰고 싶은 유혹을 뿌리쳐라. 문자열은 잘못 사용하면 번거롭고, 덜 유연하고, 느리고, 오류 가능성도 크다.

### 문자열을 쓰지 않아야 하는 경우

- 문자열은 **다른 값** **타입**을 대신하기에 적합하지 않다.
- 문자열은 **열거 타입**을 대신하기에 적합하지 않다.
- 문자열은 **혼합 타입**을 대신하기에 적합하지 않다
- 문자열은 **권한** 표현하기에 적합하지 않다.

## 아이템63 : 문자열 연결은 느리니 주의하라

> 성능에 신경 써야 한다면 많은 문자열을 연결할 때는 문자열 연결 연산자(+)를 피하고, 대신 StringBuilder를 사용하라.

- 문자열 연결 연산자(+)로 문자열 n개를 잇는 시간은 n²에 비례한다.
⇒ 문자열은 불변이기 때문에 두 문자열을 연결하는건 양쪽의 내용을 복사해야하기 때문이다.
- 성능을 포기하고 싶지 않다면 StringBuilder를 사용하라. (String보다 최소 5.5배 빠르다.)

## 아이템64 : 객체는 인터페이스를 사용해 참조하라

> 적합한 인터페이스만 있다면 매개변수뿐 아니라 반환값, 변수, 필드를 전부 인터페이스 타입으로 선언하라. **인터페이스를 타입으로 사용하는 습관을 길러두면 프로그램이 훨씬 유연해진다.** 적합한 인터페이스가 없다면 클래스의 계층구조 중 필요한 기능을 만족하는 가장 덜 구체적인 (상위의) 클래스를 타입으로 사용하자.

### 클래스 참조하야하는 클래스

- String, BigInteger 와 같은 값 클래스
- 클래스 기반으로 작성된  프레임워크가 제공하는 객체. 그래도 구현 클래스보다는 기반 클래스를 참조하는 것이 좋다.
ex) `java.io` 패키지, `OutputStream` 등
- 특별한 메서드를 제공하는 클래스
ex) `PriorityQueue`

## 아이템65 : 리플렉션보다는 인터페이스를 사용하라

> 리플렉션은 복잡한 특수 시스템을 개발할 때 필요한 강력한 기능이지만, 단점도 많다. 컴파일타임에는 알 수 없는 클래스를 사용하는 프로그램을 작성한다면 리플렉션을 사용해 피해야 할 것이다. 단 , 되도록 객체 생성에만 사용하고, 생성한 객체를 이용할 때는 적절한 인터페이스나 컴파일 타임에 알 수 있는 상위클래스로 형변환해 사용해야 한다.

### 리플렉션

**리플렉션을 이용하여 프로그램에서 임의의 클래스에 접근하고, 정보를 가져올 수 있다. 더 나아가 생성자, 메서드, 필드를 조작하고 메서드를 호출 할 수 있게 해준다.**

- 리플렉션은 아주 제한된 형태로만 사용해야 단점을 피하고 이점만 취할 수 있다.
- 리플렉션은 런타임에 존재하지 않을 수 있는 다른 클래스, 메서드, 필드와의 의존성을 관리할 때 적합하다.

### 리플렉션 단점

1. 컴파일타임 타입 검사가 주는 이점을 하나도 누릴 수 없다.
2. 코드가 지저분해지고 장황해진다.
3. 성능이 떨어진다.

## 아이템66 : 네이티브 메서드는 신중히 사용하라

> 네이트브 메서드를 사용하려거든 한번 더 생각하라. 네이티브 메서드가 성능을 개선해주는 일은 많지 않다. 저수준 자원이나 네이티브 라이브러리를 사용해야만 해서 어쩔 수 없더라도 사용을 최소화하고 철저히 테스트하라.

### 자바 네이티브 인터페이스(Java Native Interface, JNI)

자바 프로그램이 네이티브 메서드(=C, C++ 같은 네이트브 프로그래밍 언어로 작성된 메서드)를 호출하는 기술이다.

### JNI 사용 목적

1. 레지스트리 같은 **플랫폼 특화 기능** 사용
2. 네이티브 코드로 작성된 기존 라이브러리 사용
3. 성능 개선을 목적으로 성능에 결정적인 영향을 주는 영약만 따로 네이티브로 언어로 작성한 경우 (권장하지 않는다.)

### 네이티브 메서드 단점

1. 네이티브 언어가 안전하지 않으므로 네이티브 메서드를 사용하는 애플리케이션도 메모리 훼손, 오류부터 안전하지 않다.
2. 자바보다 플랫폼 영향을 많이 받아서 이식성도 낮다.
3. 디버깅이 어렵다
4. GC 대상이 아니기 때문에 메모리 추적이 불가능 하다.
5. 자바코드, 네이티브 코드의 경계를 넘나 들때 비용도 추가된다.
6. 자바코드, 네이티브 코드 사이에 접착코드를 작성해야하는데 귀찮고, 가독성이 떨어진다.

## 아이템67 : 최적화는 신중히 하라

> 좋은 프로그램을 작성하다 보면 성능은 따라오게 마련이다. 하지만 시스템을 설계 할 때, 특히 API, 네트워크 프로토콜, 영구 저장용 데이터 포맷을 설계 할 때는 성능을 염두에 두어야 한다.

### 최적화에 대한 격언

- *(맹목적인 어리석음을 포함해) 그 어떤핑계보다 효율성이라는 이름 아래 행해진 컴퓨팅 죄악이 더 많다.(심지어 효율을 높이지도 못하면서) - 윌리엄 울프*
- *(전체의 97% 정도인) 자그만한 효율성은 모두 잊자. 섣부른 최적화가 만악의 근원이다. - 도널드 크누스*
- *최적화 할때는 다음 두 규칙을 따르라
첫 번째, 하지마라.
두 번째, (전문가 한정) 아직 하지마라. 다시 말해, 완전히 명백하고 최적화되지 않는 해법을 찾을때까지는 하지마라. - M.A. 잭슨*

### 좋은 설계가 성능을 최적화 시킨다.

성능 때문에 견고한 구조를 희생하지 말자. 빠른 프로그램보다는 좋은 프로그램을 작성하라.
잘 설계된 API는 성능도 좋은게 보통이다. 그러니 성능을 위해 API를 왜곡하는 건 안좋은 생각이다.

- 좋은 프로그램은 정보 은닉 원칙을 따르므로 개별 구성요소의 내부를 독립적으로 설계 할 수 있다. 시스템의 나머지에 영향을 주지 않고도 각 요소를 다시 설계 할 수 있다.
- 설계 단계에서 성능을 염두하자
- 성능을 제한하는 설계를 피하라
완성 후 가장 변경하기가 어려운 설계 요소는 컴포넌트끼리, 혹은 외부 시스템과의 소통 방식이다. (ex. API, 네트워크 프로토콜, 영구 저장용 데이터 포맷)
- API 설계 시 성능에 주는 영향을 고려하라.

### 프로파일링 도구

프로파일링 도구는 최적화 노력을 어디에 집중해야 할지 찾는데 도움을 준다.

## 아이템68 : 일반적으로 통용되는 명명 규칙을 따르라

> 표준 명명 규칙을 체화하여 자연스럽게 베어 나오도록 하자. 철자 규칙은 직관적이라 모하한 부분이 적은데 반해, 문법 규칙은 복잡하고 느슨하다.
"오랫동안 따라온 규칙과 충돌한다면 그 규칙을 맹종해서는 안된다."

***자바 명명 규칙은 크게 철자와 문법, 두 범주로 나뉜다.***

### 철자 규칙

패키지, 클래스, 인터페이스, 메서드, 필드, 타입 변수의 이름을 다룬다.

- **패키지와 모듈** 이름은 각 요소를 점(.)으로 구분하여 계층적으로 짓는다.
조직 바깥에서도 사용될 패키지라면 조직의 인터넷 도메인 이름을 역순으로 사용한다.
패키지 이름의 나머지는 해당 패키지를 설명하는 하나 이상의 요소로 이뤄진다. 각 요소는 8자 이하의 짧은 단어로 한다.
- **클래스와 인터페이스**의 이름은 하나 이상의 단어로 이뤄지며, 각 단어는 대문자로 시작한다.널리 통용되는 줄임말을 제외하고는 단어는 줄여 쓰지 않도록 한다.
- **메서드와 필드이름**은 첫 글자를 소문자로 쓴다는 점만 빼면 클래스 명명 규칙과 같다.
단 상수(=`static final`)는 예외다. 상수는 모두 대문자로 사용하고 단어 사이는 _로 구분한다.
- **지역변수**에도 다른 멤버와 비슷한 명명 규칙이 적용된다. 단 약어를 써도 좋다.
- **타입 매개변수** 이름은 보통 한 문자로 표현한다.
임의의 타입엔 T, 컬레션 원소의 타입은 E, 맵의 키와 값에는 K, V를 예외에는 X를 메서드 반환 타입에는 R을 사용한다. 그 외에 임의타입의 시퀀스에는 T, U, V 혹은 T1, T2, T3를 사용한다.

### 문법 규칙

- **객체를 생성 할 수 있는 클래스**의 이름은 보통 단수 명사나 명사구를 사용한다.
ex) Thread, PriorityQueue, Chesspiece 등
- **객체를 생성 할 수 없는 클래스**의 이름은 보통 복수형 명사로 짓는다.
ex) Collectors, Collections 등
- **인터페이스 이름**은 클래스와 똑같이 짓거나, able, ible로 끝나는 형용사로 짓는다.
ex) Collection, Comparator, Runnable, Accessible 등
- **애너테이션**은 워낙 다양하게 활용되어 지배적인 규칙이 없이 명사, 동사, 동사, 전치사, 형용사가 두루 쓰인다.
- **어떤 동작을 수행하는 메서드**의 이름은 동사나 동사구로 짓는다.
ex) append, drawImage 등
- **boolean 값을 반환하는 메서드**라면 is, has로 시작하고 명사나 명사구, 혹은 형용사로 기능하는 아무 단어나 구로 끝나도록 짓는다.
ex) isDigit, isProbablePrime, isEmpty 등
- **반환 타입이 boolean이 아니거나 해당 인스턴스의 속성을 반환하는 메서드**의 이름은 보통 명사, 명사구, 혹은 get으로 시작하는 동사구로 짓는다.
ex) size, hashCode, getTime 등
- **객체의 타입을 바꿔서, 다른 타입의 또 다른 객체를 반환하는 인스턴스 메서드**의 이름은 toType 형태로 짓는다.
ex) toString, toArray 등
- **객체의 내용을 다른 뷰로 보여주는 메서드**의 이름은 asType으로 짓는다.
ex) asList 등
- **객체의 값을 기본 타입 값으로 반환하는 메서드**의 이름은 보통 typeValue 형태로 짓는다.
ex) intValue 등
- **정적 팩터리의 이름**은 from, of, valueOf, instance, getInstance, newInstance, getType, newType을 흔히 사용한다.