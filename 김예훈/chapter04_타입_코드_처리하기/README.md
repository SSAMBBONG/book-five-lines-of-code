> # 타입 코드 처리하기

## 규칙: if 문에서 else를 사용하지 말 것

프로그램에서 이해하지 못하는 타입을 검사할 때만 else를 사용한다.

if-else문은 하드코딩된 결정이므로 코드의 유연성이 떨어진다. 즉 컴파일 시 동작이 결정되므로 재컴파일 없이 수정이 불가능하다. 이는 추가에 의한 변경을 방해한다.

## 리팩터링 패턴: 클래스로 타입 코드 대체

열겨형을 인터페이스로 변환하고 열거형의 값을 클래스로 만든다. 그러면 각 값에 속성과 기능을 추가할 수 있게 된다.

리팩토링 전

```ts
enum Input {
  RIGHT,
  LEFT,
  UP,
  DOWN,
}

function handleInput(input: Input) {
  if (input === Input.LEFT) moveHorizontal(-1);
  else if (input === Input.RIGHT) moveHorizontal(1);
  else if (input === Input.UP) moveVertical(-1);
  else if (input === Input.DOWN) moveVertical(1);
}
```

리팩토링 후

```ts
interface Input {
    isRight(): boolean;
    isLeft(): boolean;
    isUp(): boolean;
    isDown(): boolean;
}

class Right implements Input {
    isRight() { return true; }
    isLeft() { return false; }
    isUp() { return false; }
    isDown() { return false; }
}

class Left implements Input { ... }
class Up implements Input { ... }
class Down implements Input { ... }

function handleInput(input: Input) {
    if (input.isLeft())
        moveHorizontal(-1);
    else if (input.isRight())
        moveHorizontal(1);
    else if (input.isUp())
        moveVertical(-1);
    else if (input.isDown())
        moveVertical(1);
}
```

## 리팩터링 패턴: 클래스로 코드 이관

앞선 '클래스로 타입 코드 대체' 리팩터링 패턴과 함께 사용하면 if-else 문 대신 추가에 의한 변경이 가능해진다.

기능을 인터페이스에서 클래스로 옮김으로써 if 문을 제거하고 기능을 데이터에 더 가까이 이동시킬 수 있다.

리팩토링 후

```ts
interface Input {
  // ...
  handle(): void;
}

class Left implements Input {
  // ...
  handle() {
    moveHorizontal(-1);
  }
}
class Right implements Input {
  // ...
  handle() {
    moveHorizontal(1);
  }
}
class Up implements Input {
  // ...
  handle() {
    moveVertical(-1);
  }
}
class Down implements Input {
  // ...
  handle() {
    moveVertical(1);
  }
}

function handleInput(input: Input) {
  input.handle();
}
```

## 리팩터링 패턴: 메서드의 인라인화

이 책의 두 가지 중요한 요소는 코드 추가(클래스를 지원하기 위해)와 코드 제거이다.

이 리팩터링 패턴은 가독성에 도움이 되지 않는 코드, 그 중에서도 메서드를 제거한다.

메서드가 한 줄만 있는 경우에는 메서드를 인라인화한다.

하지만 '호출 또는 전달, 한 가지만 할 것' 규칙을 어길 경우에는 인라인화해서는 안 된다.

## 리팩터링 패턴: 메서드 전문화

개발자들은 일반화와 재사용에 대한 욕구가 있지만, 어떠한 경우에는 메서드의 책임이 모호해지고 다양한 위치에서 코드를 호출할 수 있다는 문제가 생긴다.

따라서 메서드를 특정 상황에만 사용할 수 있게 전문화하면 더 적은 위치에서 호출하게 되고, 그럴수록 필요성이 없어질 때 더 빨리 제거할 수 있다.

또한 포괄적이지 않고 상황을 특정하므로 가독성을 더 향상시킬 수 있다.

리팩터링 전

```ts
function canMove(start: Tile, end: Tile, dx: number, dy: number) {
return dx * abs(start.x - end.x) ===  dy * abs(start.y - end.y)
  ||   dy * abs(start.x - end.x) ===  dx * abs(start.y - end.y);
}

if (canMove(start, end, 1, 0)) // 룩
```

리팩터링 후

```ts
function rookCanMove(start: Tile, end: Tile) {
  return abs(start.x - end.x) === 0
      || abs(start.y - end.y) === 0;
}

if (rookCanMove(start, end))
```

## 규칙: switch를 사용하지 말 것

switch는 두 가지 문제가 있다.

하나는 default를 사용할 수 있다는 것이다. 새로운 값을 추가해야 하는데 하지 않았을 때, 일부로 추가하지 않은 것인지, default에 포함시킨 것인지 컴파일 타임에 알 수 없다.

다른 문제는 break 키워드를 만나기 전까지 케이스를 연속해서 실행한다는 것이다. 이때 break 키워드를 누락한다면 다른 값이 나오지만 이를 쉽게 알아차리기 어렵다.

## 규칙: 인터페이스에서만 상속받을 것

인터페이스 대신 추상 클래스를 사용함으로써 코드의 중복을 피할 수 있다.

하지만 인터페이스를 사용해야 나중에 새로운 클래스를 개발할 때 속성을 잊어버리거나 메서드를 잘못 오버라이드하지 않을 수 있다.

이 규칙에 대한 내용은 5장 '전략 패턴의 도입'에서 더 자세히 다룬다.

## 리팩터링 패턴: 삭제 후 컴파일하기

이 리팩터링 패턴은 인터페이스에서 사용하지 않는 메서드를 삭제한 후 컴파일 오류가 발생하지 않는지 확인하는 방법이다.

애플리케이션에서 사용되지 않는 코드는 컴파일과 분석이 느려지고 테스트를 어렵게 만들기 때문에 빨리 제거할수록 비용이 줄어들게 된다.
