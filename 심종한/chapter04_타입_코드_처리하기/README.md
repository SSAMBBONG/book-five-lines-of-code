# lecture04 - 타입 코드 처리하기

## 4.1 간단한 if문 리팩토링

if문은 검사(check)로, if-esle문은 의사결정(decision)으로 간주된다.

#### if문에서 else를 사용하지 말 것

이를 위해 우선 외부 데이터 타입을 내부에서 제어 가능한 데이터 타입으로 맵핑해야 한다.

#### 인터페이스로 추사화하고 구현체를 두어 사용할 것

열거형 값들을 클래스 타입으로 변경하면 새로운 속성을 추가할 때 유리하다.
또한 특정 값과 관련된 기능을 특성에 맞게 만들 수 있다.

```java
class Right implements Input {
	isRight() { return true; }
	isLeft() { return false; }
	isUp() { return false; }
	isDown() { return false; }
}

public void handleInput(Input input) {
	if (input.isRight()) {
		moveHorizontal(1);
	}
	...
}
```

#### 클래스로 코드 이관하기

새로운 방향의 입력이 추가되면 인터페이스에 관련 메서드가 추가될 것이며 모든 구현체에서도 메서드 오버라이드가 필요하다.
handleInput() 메서드의 매개변수는 Input과 관련있다.
이는 코드가 해당 클래스에 포함되어야 함을 의미한다.

아래처럼 handleInput() 메서드를 Input 구현체 내부로 이동하면 함수의 매개변수를 제거할 수 있다.

```java
interface Input {
  ...
	void handle();
}

class Right implements Input {

	public void handle() {
		moveHorizontal(1);
		...
	}

}

class Left implements Input {

	public void handle() {
		moveHorizontal(-1);
		...
	}

}

public void handleInput(Input input) {
	input.handle();
}
```

결과적으로 handleInput() 메서드의 조건문이 사라지고 input 인터페이스의 메서드를 호출하도록 코드가 개선되었다.

특정 값과 연결된 기능이 값에 해당하는 클래스 내부로 이동하면서 불변속성을 지역화하게 되었다.

#### 불필요한 메서드 인라인화

가독성에 도움이 되지 않는 메서드를 제거한다.

메서드가 불필요한 곳에서 사용되지 않을 수 있다.

## 4.2 긴 if문의 리팩토링

우선 중간에 긴 if-else문을 메서드로 분리한다.

4.1절의 내용에서 봤듯이 enum 상수를 인터페이스로 변경하자.

```java

// enum 값과 일치성 검사
if (map[x][y] == Tile.FLUX) {
	...
}

// 인터페이스를 사용하여 추상화, 메서드로 검사
if (map[x][y].isFlux()) {
	...
}
```

#### 일반성 제거

인터페이스로 추상화하고 조건문에서 타입을 특정하지 않으면 구현체마다 다르게 동작하도록 만들 수 없게 된다.

너무 일반적이라면 코드의 유연성이 떨어지고 변경하기 어려워질 수 있다.

어떤 유형의 구현체인지 체크하는 로직을 추가하여 이 문제를 해결할 수 있다.

```java
// 너무 일반적인 경우
public void remove(Tile tile) {
	for (int i = 0; i < N; i++ ) {
		for (int j = 0; j < M; j++) {
			if (map[i][j] == tile) {
				map[i][j] = new Air();
			}
		}
	}
}

// 구체 타입을 체크하도록 변경
public void remove() {
	for (int i = 0; i < N; i++ ) {
		for (int j = 0; j < M; j++) {
			if (map[i][j].isFlux()) {
				map[i][j] = new Air();
			}
		}
	}
}
```

이렇게 일반화를 줄이고 특정화한 버전의 함수를 도입하는 과정을 `메서드 전문화` 라고 부른다.

#### switch를 사용하지 말 것

default 케이스가 없고 모든 case에 반환값이 있는 경우가 아니라면 switch를 사용하지 말자.

switch를 사용할 경우 무엇을 처리하고 무엇을 처리하지 않을 것인지는 불변속성이다.
그러나 새로운 값을 추가하는 경우 불변속성이 유효한지 컴파일러로 판단할 수 없다.
또한 break문을 사용하지 않아 폴스루(fall-through) 문제도 발생한다.

## 4.3 코드 중복 처리

여러 곳에서 중복으로 사용하는 코드를 인터페이스에 선언하고 구현체마다 서로 다르게 동작할 수 있도록 변경하여 중복을 제거할 수 있다.

#### 인터페이스 대신 추상 클래스를 사용할 수 없을까?

사용할 수 있다.
개발자가 직접 구현해야 하는 부분은 추상 메서드로 만들고 재정의하도록 하면 된다.
그러나 단점이 더 많다.

자식 클래스가 부모 클래스의 모든 세부사항을 알게 된다.
또한 사용하지 않는 추상 메서드로 자식 클래스가 재정의해야 한다.
