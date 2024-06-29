> # Ch 03. 긴 코드 조각내기

## 3.1 첫 번째 규칙: 왜 다섯 줄인가?

### 규칙: 다섯 줄 제한

메서드는 '{' 와 '}'를 제외하고 5줄 이상이 되어서는 안 된다.

코드 한 줄은 하나의 if, for, while 또는 세미콜론으로 끝나는 모든 것을 말한다.

20줄의 메서드가 있으면 첫 10줄과 나머지 10줄을 각각 도우미 메서드로 만든다. 그러면 원래의 메서드는 두 줄이 된다. 모든 메서드가 두 줄이 될 때까지 이를 반복한다.

5줄이라는 수치가 중요한 게 아니라, 제한이 있다는 것이 중요하다.

20줄인 하나의 메서드보다 5줄인 4개의 메서드가 훨씬 빠르고 이해하기 쉽다. 각 메서드의 이름으로 코드의 의도를 전달할 수 있기 때문이다.

메서드의 이름을 지정하는 것은 주석을 넣는 것과 같다. 또한 작은 메서드의 이름이 큰 함수의 이름을 정하는 데 도움을 줄 수 있다.

## 3.2 함수 분해를 위한 리팩터링 패턴 소개

### 리팩터링 패턴: 메서드 추출

메서드 추출은 한 메서드의 일부를 가져다 새로운 메서드로 만드는 것이다.

#### 절차

---

1. 추출할 줄의 주변을 빈 줄로 표기하거나 주석으로 표시한다.
2. 원하는 이름으로 새로운 빈 메서드를 만든다.
3. 그룹의 맨 위에서 새로운 메서드를 호출한다.
4. 그룹의 모든 줄을 잘라내어 새로운 메서드의 본문에 붙여 넣는다.
5. 컴파일한다.
6. 매개변수를 도입하여 호출하는 쪽의 오류를 발생시킨다.
7. 이러한 매개변수 중 하나(p라고 가정)를 반환해야 할 경우:
   - 새로운 메서드의 마지막에 <b>return p;</b>를 추가한다.
   - 새로운 메서드를 호출하는 쪽에서 <b>p = newMethod(...)</b>와 같이 반환 값을 할당한다.
8. 컴파일한다.
9. 호출 시 인자를 전달해 오류를 잡는다.
10. 사용하지 않는 빈 줄과 주석을 제거한다.

이 절차는 기존 기능을 손상시키지 않으므로 안전함을 보장한다.

변경 전

```ts
function minimum(arr: number[][]) {
  let result = Number.POSITIVE_INFINITY;
  for (let x = 0; x < arr.length; x++)
    for (let y = 0; y < arr[x].length; y++) if (result > arr[x][y]) result = arr[x][y];
  return result;
}
```

변경 후

```ts
function minimum(arr: number[][]) {
  let result = Number.POSITIVE_INFINITY;
  for (let x = 0; x < arr.length; x++)
    for (let y = 0; y < arr[x].length; y++) result = min(result, arr, x, y);
  return result;
}
function min(result: number, arr: number[][], x: number, y: number) {
  if (result > arr[x][y]) result = arr[x][y];
  return result;
}
```

## 3.3 추상화 수준을 맞추기 위한 함수 분해

### 규칙: 호출 또는 전달, 한 가지만 할 것

하나의 함수 내에서는 객체에 있는 메서드를 호출하거나 객체를 다른 함수의 인자로 전달하는 것 중 하나만 해야 한다.

객체를 직접 조작하는 낮은 수준의 추상화와 다른 함수에 객체를 인자로 전달하는 높은 수준의 추상화가 공존하면 매서드 이름 사이의 불일치로 가독성이 떨어질 수 있다.

변경 전

```ts
function draw() {
  let canvas = document.getElementById("GameCanvas") as HTMLCanvasElement;
  let g = canvas.getContext("2d");
  g.clearRect(0, 0, canvas.width, canvas.height);
  drawMap(g);
  drawPlayer(g);
}
```

변경 후

```ts
function createGraphics() {
  let canvas = document.getElementById("GameCanvas") as HTMLCanvasElement;
  let g = canvas.getContext("2d");
  g.clearRect(0, 0, canvas.width, canvas.height);
  return g;
}
function draw() {
  let g = createGraphics();
  drawMap(g);
  drawPlayer(g);
}
```

### 좋은 함수 이름의 속성

- 함수의 의도를 설명해야 한다.
- 함수가 하는 모든 것을 담아야 한다.
- 도메인에서 일하는 사람이 이해할 수 있도록 작업 중인 도메인에서 사용하는 단어를 사용한다.

### 너무 많은 일을 하는 함수 분리하기

#### 규칙: if 문은 함수의 시작에만 배치

if문 역시 하나의 작업이므로 하나의 메서드로 추출한다.

변경 전

```ts
function handleInputs() {
  while (inputs.length > 0) {
    let current = inputs.pop();
    if (current === Input.RIGHT) moveHorizontal(1);
    else if (current === Input.LEFT) moveHorizontal(-1);
    else if (current === Input.DOWN) moveVertical(1);
    else if (current === Input.UP) moveVertical(-1);
  }
}
```

변경 후

```ts
function handleInputs() {
  while (inputs.length > 0) {
    let current = inputs.pop();
    handleInput(current);
  }
}
function handleInput(input: Input) {
  if (current === Input.RIGHT) moveHorizontal(1);
  else if (current === Input.LEFT) moveHorizontal(-1);
  else if (current === Input.DOWN) moveVertical(1);
  else if (current === Input.UP) moveVertical(-1);
}
```
