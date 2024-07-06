# 타입 코드 처리하기

앞선 내용에서 메서드 추출로 추출할 수 없는 `handleInput`함수를 만들었다.

```java
function handleInput(input: Input) {
 if (input === Input.LEFT) moveHorizontal(-1);
 else if (input === Input.RIGHT) moveHorizontal(1);
 else if (input === Input.UP) moveVertical(-1);
 else if (input === Input.DOWN) moveVertical(1);
}
```

## 간단한 if문 리팩터링

### 규칙 : if문에서 else를 사용하지 말 것

- if-else를 사용하면 코드에서 결정이 내려지는 지점을 고정하게 된다.
  ![](https://velog.velcdn.com/images/bapdung/post/99ec9994-735c-485e-b53c-922cf86f6388/image.png)

#### 스멜

- **이른 바인딩** : if-else 같은 의사결정 동작은 컴파일 시 처리되어 애플리케이션 고정되며 재컴파일 없이 수정할 수 없다.
- 이것의 반대는 코드가 실행되는 순간에 동작 결정되는 **늦은 바인딩**이다.
- 이른 바인딩은 if문을 수정해야 변경할 수 있기 때문에 추가에 의한 변경을 방해한다.

### 규칙 적용

- input을 열거형(enum)에서 인터페이스로 바꾼다.(값들을 클래스로 변환)
- 값이 객체가 되면 if 구문 내의 코드를 각 클래스의 메서드로 옮길 수 있다.

1. 인터페이스 도입
   ![](https://velog.velcdn.com/images/bapdung/post/729c11be-b7ea-46e2-9be7-2fc92fa1144f/image.png)
2. 새로운 클래스
   ![](https://velog.velcdn.com/images/bapdung/post/70a06328-2f3b-468f-a8a6-83dd84f8a285/image.png)
3. 열거형 이름 변경
   ![](https://velog.velcdn.com/images/bapdung/post/69a1fa8e-ec99-4517-a35e-05ba6fd9e984/image.png)
4. 매개변수 타입 Input에서 Input2로 변경 후 일치여부 검사를 새로운 메서드로 대체
   ![](https://velog.velcdn.com/images/bapdung/post/793506e7-26d7-4e0d-bf69-4a3037321058/image.png)
5. 결과
   ![](https://velog.velcdn.com/images/bapdung/post/d79470ec-4b60-447e-837a-e74f46f9dce7/image.png)

### 리팩터링 패턴 : 클래스로 타입 코드 대체

- 열거형을 인터페이스로 변화하고 열거형의 값들은 클래스가 된다.
- 타입 코드를 볼 때는 항상 열거형으로 변환
  ![](https://velog.velcdn.com/images/bapdung/post/92db4505-7e1b-4a6a-b9d6-45548a5c64b2/image.png)

초기코드
![](https://velog.velcdn.com/images/bapdung/post/cc6a5313-688f-46d9-a30c-26d5ece4117c/image.png)

1.  임시 이름을 가진 새로운 인터페이스를 도입합니다. 인터페이스에는 열거형(enum)의 각 값에 대한 메서드가 있어야 합니다.
    ![](https://velog.velcdn.com/images/bapdung/post/bc571784-1f95-4388-966b-d88a648b4b52/image.png)
2.  열거형의 각 값에 해당하는 클래스를 만듭니다. 클래스에 해당하는 메서드를 제외한 인터페이스의 모든 메서드는 false를 반환해야 합니다.
    ![](https://velog.velcdn.com/images/bapdung/post/32ae8e9c-c3ab-4ac8-aaf5-41b5320afe31/image.png)
3.  열거형의이름을다른이름으로바꿉니다.그렇게하면컴파일러가열거형을사용하는모든위치에서오류를발생시킵니다.
    ![](https://velog.velcdn.com/images/bapdung/post/1be433ba-f9ee-4413-88d1-d9a12a55ecbd/image.png)

4.  타입을 이전 이름에서 임시이름으로 변경하고 일치성검사를 새로운 메서드로 대체합니다.
    ![](https://velog.velcdn.com/images/bapdung/post/84efc5a0-2093-4af5-8d08-9f00acc18524/image.png)

5.  남아있는 열거형 값에 대한 참조 대신 새로운 클래스를 인스턴스화하여 교체합니다.
    ![](https://velog.velcdn.com/images/bapdung/post/8ea2401a-e963-4074-ac7f-4a5b628681ad/image.png)

6.  오류가 더 이상 없으면 인터페이스의 이름을 모든 위치에서 영구적인것으로 바꿉니다.
    ![](https://velog.velcdn.com/images/bapdung/post/eaa7b668-007f-4885-b500-d4eb505b03ed/image.png)

### 불필요한 메서드 인라인화

1. 메서드의이름을handleInput2로 변경합니다. 그러면 함수를 사용하는모든곳에서컴파일러오류가발생합니다.
2. 함수내용인input.handle();을 복사합니다. input이 매개변수라는 것에 유의하십시오.
3. 우리는이함수를한곳에서만사용하는데,handleInput 함수를호출하는곳을복사한내용으로변경합니다.
   ![](https://velog.velcdn.com/images/bapdung/post/31031430-1781-41f4-a285-22a91ea2ad07/image.png)

### 메서드의 인라인화

- 더 이상 가독성에 도움이 되지 않는 메서드를 제거한다. 메서드에서 이를 호출하는 모든 곳으로 코드를 옮긴다.
- 메서드의 인라인화를 수행할 때는 모든 호출 측을 수정하여 원래의 메서드를 제거한다.
- 고려사항
  - 메서드가 한 줄만 있는 경우 이 작업 수행한다. 다섯줄 제한 규칙을 지키기 위해서다. 한 줄인 메서드를 인라인화하면 그 규칙을 위반하지 않는다.
  - 메서드가 인라인화하기에 너무 복잡하지 않은지의 여부도 판단해야 한다.

### 메서드 전문화

- 좀 더 전문화된 메서드는 더 적은 위치에서 호출되어 필요성이 없어지면 더 빨리 제거할 수 있다.

1. 전문화하려는 메서드를 복제한다.
2. 메서드 중 하나의 이름을 새로 사용할 메서드의 이름으로 변경하고 전문화하려는 매개변수를 제거 또는 교체한다.
3. 매개변수 제거에 따라 메서드를 수정해서 오류가 없도록한다.
4. 이전의 호출을 새로운 것을 사용하도록 변경한다.
   ![](https://velog.velcdn.com/images/bapdung/post/37ff9137-ab68-4c0f-b61d-91276f67d61a/image.png)
   ![](https://velog.velcdn.com/images/bapdung/post/a4cbc986-049e-485e-959f-e7a3a1a6b48f/image.png)

- 주석 필요없이 함수명만으로 파악이 가능해진다.

### switch를 사용하지 말 것

- default 케이스가 없고 모든 case에 반환 값이 있는 경우가 아니라면 switch를 사용하면 안된다.
- switch는 두 가지 **편의성**을 허용하기에 문제가 있다.
  1. switch로 case를 분석할 때 모든 값에 대한 처리를 실행할 필요가 없다. default로 중복없이 여러값을 지정할 수 있어서 컴파일러입장에서는 우리가 새로 추가한 값의 처리를 잊은 것인지, default에 지정하고자 한 것인지 구분하기 어렵다.
  2. break 키워드를 만나기 전까지 케이스를 연속해서실행하는 폴스루(fall-through)로직이라는 점이다. break 키워드 누락 가능성이 존재한다.
     ![](https://velog.velcdn.com/images/bapdung/post/d6581ca0-5989-4c6d-8dc2-ef185bc1f863/image.png)

#### 인터페이스에서만 상속받을 것

- 상속은 오직 인터페이스를 통해서만 받아야한다.
- 추상 클래스를 사용할 때는 커플링(결합)이 유발될 수 있다.

#### 클래스에 있는 코드의 중복은 다 무엇일까?

- 복제된 코드가 있고 한 곳에서 변경되면 두 가지 다른 기능이 존재하게 된다. 코드의 중복은 분기(divergence)를 조장하기 때문에 나쁘다.

>

### 요약

1. if 문에서 else 사용하지 않기:

- else는 프로그램의 가장자리에만 있어야 하며, 낮은 수준의 제어 흐름 연산자입니다.
  애플리케이션의 핵심에서는 else를 사용하지 않기 위해, 타입 코드 대체 및 코드 이관 리팩터링 패턴을 사용합니다.

2. switch 사용하지 않기:

- switch 역시 프로그램의 가장자리에만 사용되어야 하며, 낮은 수준의 제어 흐름 연산자입니다.
- switch와 연속된 else if 구문을 대신해, 높은 수준의 클래스와 메서드로 대체합니다.

3. 메서드 관련 규칙 및 리팩터링 패턴

- 지나치게 일반화된 메서드 문제 해결
- 지나치게 일반화된 메서드는 리팩터링을 방해할 수 있습니다.
- 이러한 경우, 메서드 전문화 리팩터링 패턴을 사용하여 불필요한 일반성을 제거합니다.

4. 인터페이스에서만 상속받기

- 추상 클래스와 클래스 상속을 사용해 코드를 재사용하지 않습니다.
- 클래스 상속은 불필요하게 긴밀한 커플링을 발생시키므로, 인터페이스만을 통해 상속받습니다.

5. 정리를 위한 리팩터링 패턴

- 메서드의 인라인화
  - 더 이상 가독성에 도움이 되지 않는 메서드는 인라인화하여 코드의 간결함을 유지합니다.
- 삭제 후 컴파일하기
  - 사용되지 않거나 불필요한 메서드는 삭제 후 컴파일하여 코드의 정리를 도모합니다.
