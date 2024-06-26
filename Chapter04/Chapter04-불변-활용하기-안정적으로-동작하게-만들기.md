# Chapter04. 불변 활용하기:안정적으로 동작하게 만들기
## 요약 정리
* 재할당은 변수의 의미를 바꿔 추측하기 어렵게 만든다.
  * 불변 변수로 만들어 재할당을 막는다.
  * 매개변수를 변경하면 값의 의미가 바뀔 수 있으므로 매개변수도 불변으로 만든다.
* 함수가 영향을 주거나 받을 수 있는 범위를 한정하여 부수 효과로 예상치 못한 동작을 막는다.
* 또는 불변으로 만들어 부수 효과 여지 자체를 없애서 예상치 못한 동작을 막는다.
* 기본적으로 불변으로 설계하되, 대량의 데이터, 이미지를 처리하는 등 성능이 중요한 경우에는 가변으로 설계한다.
  * 변수를 가변으로 만들었다면 상태를 변경하는 메서드, 뮤테이터(mutater)를 만들어 올바른 상태로만 변경하도록 한다.
* 코드 외부와 데이터 교환은 국소화하여 외부에 있는 상태로 받을 수 있는 영향을 최소화한다.
  * 레포지토리 패턴: DB의 영속화를 캡슐화하는 디자인 패턴

## 재할당
* 변수의 값이 다시 할당하는 것을 **재할당** 또는 **파괴적 할당**이라고 한다.
* 중간에 값을 할당하면(의미가 바뀌면) 코드를 보는 사람은 햇갈릴 수밖에 없다.
* 변수 하나를 재활용하지 않고, 계속해서 새로운 변수를 만들어 사용하면 재할당을 피할 수 있다.

### 불변 변수로 만들어서 재할당 막기
```java
void doSomething() {
    final int value = 100;
    value = 200;    // 컴파일 오류
}
```

### 매개변수도 불변으로 만들기
* 매개변수를 변경하면 값의 의미가 바뀔 수 있다.
* 매개변수에 어떠한 연산을 하고 싶다면 불변 지역 변수를 만들고 이를 이용해서 연산한다.
```java
void addPrice(int productPrice) {
    productPrice = totalPrice + productPrice;
    // ...
}
```

### 부수 효과의 단점
* 함수의 **부수 효과**: 함수가 매개변수를 전달받고, 값을 리턴하는 것 이외에 **외부 상태를 변경하는 것**
  * 주요 작용: 함수가 매개변수를 전달받고, 값을 리턴하는 것
  * 부수 효과: 주요 작용 이외에 상태 변경을 일으키는 것
    * 인스턴스 변수 변경
    * 전역 변수 변경
    * 매개변수 변경
    * 파일 읽고 쓰기 조작

### 함수의 영향 범위 한정하기
* 예상치 못한 동작을 막으려면, 함수가 영향을 주거나 받을 수 있는 범위를 한정하는 것이 좋다.
* 다음 항목들을 만족하도록 설계한다.
  * 데이터(상태)는 매개변수로 받는다.
  * 상태를 변경하지 않는다.
  * 값은 함수의 리턴 값으로 돌려준다.
* 인스턴스 변수는 불변으로 만들어 영향이 전달되지 않게 만들면, 메서드에서 인스턴스 변수를 사용하는 것은 괜찮다.
* 객체 지향의 언어는 함수의 부수 효과로 인한 범위를 클래스 내부까지 허용하는 것이 일반적이다.

### 불변으로 만들어서 예기치 못한 동작 막기
* '주의해서 코드를 작성할 테니 불변이 아니어도 괜찮을거다.' 라는 생각은 스스로를 너무 맹신하는 것
* 기능 변경 때에 의도하지 않게 부수 효과가 있는 함수가 만들어져, **예상하지 못한 동작을 일으킬 가능성은 항상 존재**

## 불변과 가변은 어떻게 다루어야 할까
### 기본적으로 불변으로
* 불변으로 만들때 장점
  * 변수의 의미가 변하지 않고, 혼란을 줄일 수 있다.
  * 동작이 안정적이게 되고, 결과를 예측하기 쉽다.
  * 코드의 영향 범위가 한정적이므로, 유지 보수가 편리해진다.
* 최근 등장하는 프로그래밍 언어는 불변이 디폴트가 되도록 만들어져 있다.

### 가변으로 설계해야 하는 경우
* **성능**이 중요한 경우
  * 대량의 데이터를 빠르게 처리해야 하는 경우
  * 이미지를 처리해야 하는 경우
  * 리소스에 제약이 큰 임베디드 소프트웨어를 다루는 경우
* 불변인 경우에는 인스턴스를 새로 생성해야 해서, 만약 크기가 큰 인스턴스를 새로 생성하면서 시간이 오래 걸려 성능 상의 문제가 있다면, 불변보다는 가변을 사용하는 것이 좋다.
* **스코프가 국소적**인 경우
  * 반복문 카운터

### 상태를 변경하는 메서드
* 상태를 변화시키는 메서드를 **뮤테이터**(mutater)라고 한다.

### 코드 외부와 데이터 교환은 국소화하기
* 파일을 읽고 쓰는 I/O 조작은 코드 외부의 상태에 의존한다. 애플리케이션도 거의 필수로 데이터베이스를 사용한다.
* 파일이나 데이터베이스는 코드 외부에 있는 상태이다.
* 코드 내부에서는 이러한 외부 동작을 제어할 수 없다.
* 코드 외부와 데이터 교환을 국소화하는 방법으로 레포지토리 패턴이 있다.
  * 레포지토리 패턴은 데이터베이스의 영속화를 캡슐화하는 디자인 패턴이다.
