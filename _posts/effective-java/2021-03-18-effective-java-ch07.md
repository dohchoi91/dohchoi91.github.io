---
title: "07.람다와 스트림"
subtitle: "아이템42 ~ 아이템48"
permalink: /notes/effective-java/ch07
author_profile: true
date: 2021-03-18
categories:
  - 이펙티브 자바
tags:
  - java
last_modified_at: 2021-03-18
---

**자바8 에서 함수형 인터페이, 람다, 메서드 참조라는 개념이 추가 되면서 함수 객체를 더 쉽게 만들 수 있게 되었다. 그리고 스트림 API까지 추가되어 데이터 원소의 시퀀스 처리를 라이브러리 차원에서 지원하기 시작됬다.**

## 아이템42 : 익명 클래스보다는 람다를 사용하라

> 익명 클래스는 (함수형 인터페이스가 아닌) 타입의 인스턴스를 만들 때만 사용하라.

### 함수객체

- 자바에서 함수타입을 표현 할 때 추상 메서드를 하나만 담은 인터페이스의 인스턴스를 지칭. 특정 함수나 동작을 나타내는데 사용
- 자바8부터 함수객체를 람다식으로 만들어 사용 가능

### 람다식

```java
Collections.sort(words, (s1,s2) -> Integer.comapre(s1.length(), s2.length());
```

- 타입을 명시해야 코드가 더 명확할 때만 제외하고는, 람다의 모든 매개변수 타입은 생략하자.
- 이름이 없기 때문에 문서화 하지 못한다. 따라서 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 말아야 한다.
- 람다는 자기 자신을 참조 할 수 없다. (`this` ⇒ 바깥 인스턴스를 지칭)
- 람다는 3줄이 넘어가면 가독성이 나빠진다.
- 직렬화 하지 말 것!

### 익명 클래스를 사용할 경우

- 추상클래스의 인스턴스를 만들 경우
- 추상 메서드가 여러 개인 인터페이스의 인스턴스를 만들경우
- `this` 사용가능

## 아이템43 : 람다보다는 메서드 참조를 사용하라

> 메서드 참조는 람다의 간단명료한 대안이 될 수 있다. 메서드 참조 쪽이 짧고 명확하다면 메서드 참조를 쓰고, 그렇지 않다면 람다를 사용하라.

### 메서드 참조 유형

- **정적 메서드 참조**
ex1) `Integer::parseInt`
ex2) `str → Integer.parseInt(str)`
- **한정적 인스턴스 메서드 참조**

    함수 객체가 받은 인수와 참조되는 메서드가 받는 인수가 똑같다.

    ex1) `Instant.now()::isAfter`
    ex2) `Instant then = Instant.now(); t → then.isAfter(t)`

- **비한정적 인스턴스 메서드 참조**

    함수 객체가를 적용하는 시점에 수신객체를 알려준다. 수신 객체 전달용 매개변수가 매개변수 목록의 첫 번째로 추가되며, 그 뒤로는 참조되는 메서드 선언에 정의된 매개변수들이 뒤따른다. 주로 스트림 파이프라인에서 매핑과 필터 함수에 쓰인다.

    ex1) `String::toLowerCase`
    ex2) `str → str.toLowerCase()`

- **클래스 생성자 메서드 참조**
ex1) `TreeMap<K, V>::new`
ex2) `() → new TreeMap<K, V>()`
- **배열 생성자 메서드 참조**
ex1) `int[]::new`
ex2) `len → new int[len]`

## 아이템44 : 표준 함수형 인터페이스를 사용하라

> API 설계 시 람다도 염두에 두어야 한다. 입력값과 반환값에 함수형 인터페이스 타입을 활용하라.

자바가 람다를 지원하면서 템플릿 메서드 패턴의 매력이 크게 줄었다. 대체 되는 현대적인 방법은 정적 팩터리나 생성자를 제공하는 거이다. 일반화 해서 말하면 함수 객체를 매개변수로 받는 생성자와 메서드를 더 많이 만들어야 한다.

- 필요한 용도에 맞는 게 있다면, 직접 구현하지 말고 표준 함수형 인터페이스를 활용하라

### `java.util.function` 대표 기본 인터페이스

1. `UnaryOperator<T>` : 반환값과 인수의 타입이 같으며 인수는 1개인 함수이다. 
2. `BinaryOperator<T>` : 반환값과 인수의 타입이 같으며 인수는 2개인 함수이다.
3. `Predicate<T>` : 인수 하나를 받아 `boolean`으로 반환하는 함수이다.
4. `Function<T,R>` : 인수와 반화타입이 다른 함수이다.
5. `Supplier<T>` : 인수를 받지 않고 값을 반환하는 함수이다.
6. `Consumer<T>` : 인수를 하나 받고 반환값은 없는 함수이다.
- 기본 인터페이스는 기본 타입인 `int`, `long`, `double`용으로 각 3개씩 변형이 생겨난다.

### Function 인터페이스에서 기본 타입을 반환하는 변형

기본적으로 입력과 결과의 타입이 항상 다르다. (총 9가지의 변형 존재)

- 입력과 결과 타입이 모두 기본 타입일 경우 접두어로 ***SrcToResult***  사용한다. (6개)
ex) `long`을 받아 `int`로 반환 : `LongToIntFunction`
- 입력이 객체 참조이고, 결과가 `int`, `long`, `double` 인 변형인 경우 입력을 매개변수화 하고 접두어로 ***ToResult***를 사용한다. (3개)
ex) `int[]`을 받아 `long`로 반환 : `ToLongFunction<int[]>`

### 기본 함수형 중 인수를 2개씩 받는 변형

- `BiPredicate<T, U>`, `BiConsumer<T, U>`, `BiFunction<T, U, R>`
- `BiFunction<T, U, R>`에서 다시 기본타입을 반환하는 변형
`ToIntBiFunction<T, U>`, `ToLontBiFunction<T, U>`, `ToDoubleBiFunction<T, U>`
- Consumer 객체에서 객체 참조와 기본타입 하나 즉 인수를 2개 받는 변형

    `ObjIntConsumer<T>`, `ObjLongConsumer<T>`, `ObjDoubleConsumer<T>`

### 전용 함수형 인터페이스 구현 시 고려사항

- 자주 쓰이며, 이름 자체가 용도를 명확히 설명해준다
- 반드시 따라야 하는 규약이 있다
- 유용한 디폴트 메서드를 제공할 수 있다.

### 주의사항

- 기본 함수형 인터페이스에 박싱된 기본 타입을 넣어 사용하지 말 것. (성능 이슈 초래)
- 직접 만든 함수형 인터페이스에는 항상 `@FunctionalInterface` 애너테이션을 사용하라
- 서로 다른 함수형 인터페이스를 같은 위치의 인수로 받는 메서드들을 다중 정의해서는 안된다.

### 아이템45 : 스트림은 주의해서 사용하라

> 스트림과 반복 중 어느 쪽이 나은지 확신하기 어렵다면 둘 다 해보고 더 나은 쪽을 택해라.

### 스트림 API

다량의 데이터 처리 작업(순차적이든, 병렬적이든)을 돕고자 자바8에 추가 되었다.
추상개념 중 핵심은 다음 두 가지이다.

1. 데이터 원소의 유한, 무한 시퀀스
2. 스트림 파이프라인 : 원소들로 수행하는 연산단계를 표현하는 개념
- 메서드 연쇄를 지원하는 플루언트 API이다.
- 다재다능하여 사실상 어떠한 계산이라도 해낼 수 있다. 제대로 사용한다면 코드가 깔끔해지지만, **잘못 사용하면 읽기 어렵고 유지보수도 힘들어진다.**
- `char` 값을 처리할 때는 스트림을 삼가는 편이 낫다.

### 스트림 파이프라인

소스 스트림에서 시작해 종단 연산으로 끝나며, 그 사이에 하나 이상의 중간 연산이 있을 수 있다.

- 각 **중간 연산(intermediate operation)**은 **스**트림을 어떠한 방식으로 변환한다. 예를 들어 각 원소에 함수를 적용하거나, 조건에 대한 필터가 가능하다. (스트림을 리턴한다.)
- **종단 연산(terminal operation)**은 마지막 중간 연산이 내놓은 스트림에 최후의 연산을 가한다. 예를 들어 원소를 정렬해 컬렉션에 담거나, 특정 원소 하나를 선택하거나, 모든 원소를 출력하는 식이다. (스트림을 리턴하지 않는다.)
- 스트림 파이프라이은 **지연 평가(lazy evaluation)**된다.
평가는 종단 연산이 호출 될 때 이뤄지며, 종단 연산에 쓰이지 않는 데이터 원소는 계산에 쓰이지 않는다. **⇒ 종단 연산이 들어가야 각 중간 연산에 적용한 연산들이 적용 된다.**
- 기본적으로 순차적으로 수행된다. parallel 메서드를 추가하면 병렬로 수행 된다. (※ 효과를 볼 수 있는 상황은 많지 않다)

### 스트림을 적용하면 좋은 예

- 원소들의 시퀀스를 일관되게 변환한다.
- 원소들의 시퀀스를 필터링 한다
- 원소들의 시퀀스를 하나의 연산을 사용해 결합한다. (더하기, 연결하기, 최소값 구하기 등)
- 원소들의 시퀀스를 컬렉션에 모은다.
- 원소들의 시퀀스에서 특정 조건을 만족하는 원소를 찾는다.

## 아이템46 : 스트림에서는 부작용 없는 함수를 사용하라

> 스트림 파이프라인 프로그래밍의 핵심은 부작용 없는 함수 객체 있다. 스트림 뿐만 아니라 관련객체에 건네지는 모든 함수객체가 사이드 이펙이 없어야 한다. (=순수함수여야 한다) 
종단 스트림을 올바로 사용하려면 수집기를 잘 알아둬야 한다. 가장 중요한 수집기 팩터리는 toList, toSet, toMap, groupingBy, joing이다.

*스트림 패러다임의 핵심은 계산을 일련의 변환으로 재구성하는 부분이다. 이 때 각 변환 단계는 가능한 한 이전 단계의 결과를 받아 처리하는 **순수함수**여야 한다.*

### 순수함수

오직 입력만이 결과에 영향을 주는 함수.
다른 가변 상태를 참조 하지 않고, 함수 스스로도 다른 상태를 변경하지 않는다.

### 스트림 패러다임의 이해

```java
Map<String, Long> freq = new HashMap<>();

// 스트림 패러다임을 이해하지 못하고 API만 사용한 코드
try(Stream<String> words = new Scanner(file).tokens()) {
	words.forEach(word -> {
		freq.merge(word.toLowerCase(), 1L, Long::sum);
	});
}

// 스트림을 제대로 활용하여 freq를 초기화한 코드
try(Stream<String> words = new Scanner(file).tokens()) {
		freq = words.collect(groupingBy(String::toLowerCase, counting());
}
```

**※ `forEach` 연산은 스트림 계산 결과를 보고할 때만 사용하고, 계산하는 데는 쓰지말자.**

### 수집기(collector)

- 수집기에서 생성하는 객체는 일반적으로 컬렉션이며, 그래서 "collector"라는 이름을 쓴다.
- 수집기를 사용하면 스트림의 원소를 손쉽게 컬렉션으로 모을 수 있다.
- 수집기는 총 3가지로 `toList()`, `toSet`, `toCollection(collectionFactory)`

## 아이템47 : 반환 타입으로는 스트림보다 컬렉션이 낫다.

> 원소 시퀀스를 반환하는 메서드를 작성할 때는, 이를 스트림으로 처리하기를 원하는 사용자와 반복으로 처리하길 원하는 사용자가 모두 있을 수 있음을 떠올리고, 양쪽을 다 만족시키려 노력하자. **컬렉션을 반환 할 수 있다면 그렇게 하라.**

반환 전부터 이미 원소들을 컬렉션에 담아 관리하고 있거나 컬력션을 하나 더 만들어도 될 정도로 원소가 적다면 ArrayList 같은 **표준 컬렉션에 담아 반환하라.** 

그렇지 않으면 전용 컬렉션을 구현할지 고민하라. 컬렉션을 반환하는 게 불가능 하면 스트림과 Iterable 중 더 자연스러운 것을 반환하라. 만약 나중에 Stream 인터페이스가 Iterable을 지원하도록 자바가 수정된다면, 그때는 안심하고 스트림을 반환하면 될 것이다.

- 스트림은 반복(iteration)을 지원하지 않는다.
- Collection 인터페이스는 Iterable의 하위 타입이고 stream 메서드도 제공하니 반복과 스트림을 동시에 지원한다. 따라서 원소 시퀀스를 반환하는 공개 API의 반환 타입에는 Collection이나 그 하위 타입을 쓰는게 일적으로 최선이다.
- 반환하는 시쿼스의 크기가 메모리에 올려도 안전할 만큼 작다면 `ArrayList`나 `HashSet`같은 표준 컬렉션 구현체를 반환하는 게 최선일 수 있지만 단지 컬렉션을 반환한다는 이유로 덩치 큰 시퀀스를 메모리에 올려서는 안된다.

## 아이템48 : 스트림 병렬화는 주의해서 적용하라

> 계산도 올바로 수행하고 성능도 빨라질 거라는 확신 없이는 스트림 파이프라인 병렬화는 시도조차 하지 말라. 스트림을 잘못 병렬화 하면 프로그램을 오작동하게 하거나 성능을 급격히 떨어뜨린다.

- 자바8 부터는 `parallel` 메서드만 한 번 호출하면 파이프라인을 병렬 실행 할 수 있는 스트림을 지원했다.
- `Stream.iterate`거나 중간 연산으로 `limit`을 쓰면 파이프라인 병렬화로는 성능 개선을 기대할 수 없다.
- **스트림을 잘못 병렬화 할 경우 성능 뿐만아니라 결과 자체가 잘못되거나 예상하지 못한 동작이 발생할 수 있다.**
- **조건이 잘 갖춰지면 parallel 메서드 호출 하나로 거의 프로세서 코어 수에 비례하는 성능 향상을 기대할 수 있다.**

### 병렬화 효율 높이는 방법

- **스트림의 소스가 ArrayList, HashMap, HashSet, ConcurrentHashMap의 인스턴스거나 배열, int 범위, long 범위 일때 병렬화의 효과가 가장좋다.**
    - 이러한 자료구조들은 모두 데이터를 원하는 크기로 정확하고 손쉽게 나눌 수 있어서 다수의 스레드에 분배하기에 좋기 때문이다. (나누는 작업은 Spliterator가 담당)
    - 원소들의 순차적으로 실행 될 때 참조지역성(locality of reference)이 뛰어나다.
- **스트림 파이프라인의 종단 연산의 동작 방식 역시 중요하다.**
    - 종단 연산 중 병렬화에 적합한 것은 축소(reduction)이다.
    축소는 파이프라인에서 만들어진 모든 원소를 하나로 합치는 작업이다.
    - 축소 관련 메서드
        - Stream의 `reduce` 메서드
        - `min`, `max`, `count` 등의 메서드
        - `anyMatch`, `allMatch`, `noneMatch`

        **※ 가변 축소를 수행하는 Stream의 `collect` 메서드는 적합하지 않다.**

### 참조 지역성

- 이웃한 원소의 참조들이 메모리에 연속해서 저장되어 있다는 뜻이다
- 참조 지역성이 낮으면 스레드는 데이터가 주 메모리에서 캐시 메모리로 전송되어 오기를 기다리며 대부분 시간을 멍하니 보내게 된다.
- 다량의 데이터를 처리하는 벌크 연산을 병렬화 할때 아주 중요한 요소로 작용한다.
- 참조 지역성이 가장 뛰어난 자료구조는 배열이다.
⇒ 데이터 자체가 메모리에 연속해서 저장 되어있기 때문.