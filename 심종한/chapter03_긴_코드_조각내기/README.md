# chapter03 - 긴 코드 조각내기

아래 원인들로 인해 코드가 지저분해질 수 있다.

- 메서드가 여러 가지 다른 일을 수행한다.
- 낮은 수준의 원시 연산을 사용한다.
- 주석, 적절한 메서드명, 변수명 같이 읽을 수 있는 텍스트가 부족하다.

## 3.2 함수 분해를 위한 리팩토링 패턴 소개

#### 메서드 추출

기능 별로 메서드를 추출하자.

## 3.3 추상화 수준을 맞추기 위한 함수 분해

#### 호출 또는 전달, 한 가지만 할 것

함수 내에서는 객체에 있는 메서드를 호출하거나 객체를 인자로 전달할 수 있지만 둘을 섞어 사용하지는 말자.

다른 함수에 인자로 전달하는 `sum` 은 고수준이다. 그러나 배열을 직접 조작하는 `arr.length` 는 저수준이다.

```java
private int average(int[] arr) {
    return sum(arr) / arr.length;
}
```

길이를 계산하는 함수를 만들어 메서드 내부의 추상화 수준을 맞추자.

```java
private int average(int[] arr) {
    return sum(arr) / size(arr);
}
```

## 3.4 좋은 함수 이름의 속성

- 함수의 의도를 설명해야 한다.
- 함수가 하는 모든 것을 담아야 한다.
- 도메인에서 사용하는 단어를 사용하여 팀원, 고객 모두가 이해할 수 있는 단어를 사용해야 한다.

## 3.5 너무 많은 일을 하는 함수 분리하기

#### if문은 함수의 시작에만 배치

함수는 한 가지 일만 해야 한다. 따라서 if문은 함수의 첫 부분에 나와야 하며 그 이후에는 아무것도 해서는 안되도록 하면 메서드는 한 가지 일만 하게 된다.

아래 코드는 한 메서드에서 두가지 일을 하고 있다.

- 숫자 반복
- 소수 판단

```java
private reportPrimes(int num) {
    for (int i = 2; i < num; i++) {
        if (isPrime(i)) {
            System.out.println(i + ' is prime');
        }
    }
}
```

하나의 함수가 하나의 작업을 처리할 수 있도록 메서드를 분리하자.

```java
private reportPrimes(int num) {
    for (int i = 2; i < num; i++) {
        reportIfPrime(i);
    }
}

private void reportIfPrime(int num) {
    if (isPrime(num)) {
        System.out.println(i + ' is prime');
    }
}
```

> else if 문은 if문과 분리할 수 없는 원자 단위로 판단한다.
