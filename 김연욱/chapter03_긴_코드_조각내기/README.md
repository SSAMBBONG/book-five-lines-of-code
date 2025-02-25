> # Chapter 03 <br><br> **긴 코드 조각내기**

## 주요 내용

- 다섯 줄 제한(Five Lines)으로 지나치게 긴 메서드 식별하기
- 세부 사항을 보지 않고 코드 작업하기
- 메서드 추출(Extract Method)로 긴 메서드 분해하기
- if 문은 함수의 시작에만 배치로 if 문 분리하기

## 규칙 : 다섯 줄 제한

- 정의 : 메서드는 `{`와 `}`를 제외하고 5줄 이상이 되어서는 안 됨
- 설명 : 문장이라고도 하는 코드 한 줄은 하나의 if, for, while 또는 세미콜론으로 끝나는 모든 것을 말함. (즉, 할당, 메서드 호출, return 같은 것이며 공백과 중괄호는 제외)

## 추상화 수준을 맞추기 위한 함수 분해

- 정의 : 함수 내에서는 객체에 있는 메서드를 호출하거나 객체를 인자로 전달할 수 있지만 둘을 섞어 사용해서는 안 됨

## 좋은 함수 이름의 속성

- 정직해야 함 (함수의 의도를 설명해야 함)
- 완전해야 함 (함수가 하는 모든 것을 담아야 함)
- 도메인에서 일하는 사람이 이해할 수 있어야 함

## 규칙 : if 문은 함수의 시작에만 배치

- 정의 : if 문이 있는 경우 해당 if 문은 함수의 첫 번재 항목이어야 함

## 요약

- 다섯 줄 제한 규칙은 메서드는 다섯 줄 이하여야 한다는 말임
- 다섯 줄 제한 규칙은 두 가지 이상의 작업을 수행하는 메서드를 식별하는 데 도움이 됨 (리팩터링 패턴인 메서드 추출을 사용해서 긴 메서드를 분해하고 메서드 이름으로 주석을 대신함)
- 호출 또는 전달, 한 가지만 할 것 규칙은 하나의 메서드 내에서 객체에 있는 메서드를 호출하거나 객체를 매개변수로 전달할 수 있지만, 둘 다 해서는 안 된다는 말임
- 메서드 이름은 투명하고 완전해야 하며 이해할 수 있어야 함 (메서드 추출을 사용하면 가독성이 향상되도록 매개변수의 이름을 바꿀 수 있음)
- if 문은 함수의 시작에만 배치 규칙은 if를 사용해 조건을 확인하는 경우 한 가지 작업만 수행하므로 메서드가 다른 작업을 수행하지 못하게 하는 것 (if 문을 분리하기 위해 메서드 추출을 사용함)