# Chapter03. 클래스 설계:모든 것과 연결되는 설계 기반
## 요약 정리
* 클래스는 클래스 하나로도 잘 동작하도록 설계해야 한다.
  * 클래스 스스로 자기 방어 임무를 할 수 있어야 소프트웨어의 품질을 높이는 데 도움이 된다.
  * 데이터 클래스는 모든 것을 다른 클래스에게 맡겼기 때문에 문제가 발생했다.
* 클래스는 최소한의 조작 방법만 외부에 제공해야 한다.
* 인스턴스 변수를 불변으로 만들어 예상치 못한 동작을 막는다.
* 엉뚱한 값을 전달하지 않도록 독자적인 자료형을 만들어 사용한다.
* 의미없는 메서드는 추가하지 않고 시스템 사양에 필요한 메서드만 정의한다.

## 클래스 단위로 잘 동작하도록 설계하기
* 클래스는 클래스 하나로도 잘 동작하도록 설계해야 한다.
* 클래스를 마음대로 조작해서 클래서 전체가 고장나는 일 없게 **최소한의 조작 방법**(메서드)만 외부에 제공해야 한다.

### 클래스의 구성 요소
* 기본적인 두 가지 요소
  * 인스턴스 변수
  * 메서드
* **잘 만들어진 클래스**의 구성 요소
  * 인스턴스 변수
  * 인스턴스 변수에 잘못된 값이 할당되지 않게 막고, 정상적으로 조작하는 메서드
* 좋은 클래스 구성은 반드시 인스턴스 변수와 메서드를 **함께 사용**해야 한다.
  * 하나라도 빠지만 안 좋음, 단 예외적으로 이러한 구성이 필요할 때도 있음
  * _1장의 데이터 클래스의 폐해_
    * 데이터 클래스는 혼자서는 아무것도 할 수 없는 미성숙한 클래스

### 자기 방어 임무
* 클래스 스스로 **자기 방어 임무**를 할 수 있어야 소프트웨어의 품질을 높이는 데 도움이 된다.
  * 데이터 클래스는 모든 것을 다른 클래스에게 맡겼기 때문에 악마들을 불러모았음

## 성숙한 클래스로 성장시키는 설계 기법
```java
class Money {
    int amount;         // 금액
    Currency currency;  // 통화 단위
}
```

* 위 Money 클래스는 인스턴스 변수만 갖고 있는 전형적인 데이터 클래스이다.

### 생성자로 확실하게 정상적인 값 설정하기
```java
class Money {
    int amount;
    Currency currency;
    
    Money(int amount, Currency currency) {
        this.amount = amount;
        this.currency = currency;
    }
}
```

* 위와 같이 생성자를 만들며 인스턴스 변수가 무조건 초기화된다.
  * 하지만, 이것만으로는 충분하지 않음!
  * 생성자 매개변수로 잘못된 값이 들어올 수도 있기 때문
```java
Money money = new Money(-10000, null);
```

* 잘못된 값이 들어오지 않게 유효성 검사를 생성자 내부에서 정의한다.
```java
class Money {
    int amount;
    Currency currency;
    
    Money(int amount, Currency currency) {
        if (amount < 0) {
            throw new IllegalArgumentException("금액은 0 이하의 값이 될 수 없습니다.");
        }
        if (currency == null) {
          throw new IllegalArgumentException("통화 단위를 지정해주세요.");
        }
          
        this.amount = amount;
        this.currency = currency;
    }
}
```

* 처리 범위를 벗어나는 조건을 메서드 가장 앞 부분에서 확인하는 코드를 **가드**(guard)라고 한다.
  * 가드를 활용하면 불필요한 요소를 메서드 앞 부분에서 제외할 수 있으므로 이어지는 로직이 간단해짐

### 계산 로직도 데이터를 가진 쪽에서 구현하기
* 데이터와 데이터를 조작하는 로직이 분리되어 있는 구조를 **응집도가 낮은 구조**라고 한다고 했다.
* 이런 구조를 막으려면 클래스를 **더 성숙하게** 만들어야 한다.
```java
class Money {
    int amount;
    Currency currency;
    
    Money(int amount, Currency currency) {
        if (amount < 0) {
            throw new IllegalArgumentException("금액은 0 이하의 값이 될 수 없습니다.");
        }
        if (currency == null) {
          throw new IllegalArgumentException("통화 단위를 지정해주세요.");
        }
          
        this.amount = amount;
        this.currency = currency;
    }
    
    // 금액 더하는 로직
    void add(int other) {
        amount += other;
    }
}
```

### 불변 변수로 만들어 예상치 못한 동작 막기
* 인스턴스 변수를 변경하는 코드는 이해하기 어렵다.
  * 변수 값이 계속 바뀌면, 값이 언제 변경되었는지, 지금 값은 무엇인지 계속 신경써야 함
```java
money.amount = originPrice;
//
// 로직
//
if (specialServiceAdded) {
    money.add(additionalServiceFee);
    if (seasonOffApplied) {
        money.amount = seasonPrice();
    }
}
```

* 이러한 문제를 막으려면 인스턴스 변수를 **불변**으로 만든다.
  * 값을 **한 번 할당하면 다시 바꿀 수 없는** 변수를 **불변 변수**라고 함
```java
class Money {
    final int amount;
    final Currency currency;
  
    Money(int amount, Currency currency) {
        if (amount < 0) {
            throw new IllegalArgumentException("금액은 0 이하의 값이 될 수 없습니다.");
        }
        if (currency == null) {
            throw new IllegalArgumentException("통화 단위를 지정해주세요.");
        }
    
        this.amount = amount;
        this.currency = currency;
    }
}
```

* `final` 수식자를 붙이면, 변수 선언 시점 또는 생성자 안에서만 값 할당 가능, 이후에는 재할당 불가능

### 변경하고 싶다면 새로운 인스턴스 만들기
* 불변인 인스턴스 변수를 변경하고 싶다면, 변경된 내용을 가진 새로운 인스턴스를 만들어 사용한다.
```java
class Money {
    // 생략
    Money add(int other) {
        int added = amount + other;
        return new Money(added, currency);
    }
}
```

### 메서드 매개변수와 지역 변수도 불변 변수로 만들기
* 기본적으로 매개변수는 변경하지 않는 것이 좋다.
* 지역 변수도 마찬가지로 중간에 값을 변경하면, 의미가 바뀔 수도 있다.

### 엉뚱한 값을 전달하지 않도록 하기
* 같은 자료형이라면, 의미가 다른 값을 잘못 전달해도 오류가 발생하지 않는다.
```java
final int ticketCount = 3;

// 가격이 아닌, 티켓의 수를 Money에 잘못 더함
money.add(ticketCount);
```

* 독자적인 자료형 (클래스 등)을 사용하면, 의미가 다른 값을 전달하는 실수를 막을 수 있다.
```java
class Money {
    // 생략
    Money add(final Money other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("통화 단위가 다릅니다.");
        }
        
        final int added = amount + other.amount;
        return new Money(added, currency);
    }
}
```

### 의미 없는 메서드 추가하지 않기
* 다음 메서드는 금액을 곱하는 메서드이다.
```java
class Money {
    // 생략
    Money multiple(final Money other) {
      if (!currency.equals(other.currency)) {
        throw new IllegalArgumentException("통화 단위가 다릅니다.");
      }
      
      final int multiplied = amount * other.amount;
      return new Money(multiplied, currency);
    }
}
```

* 금액끼리 곱하는 기능은 사용하지 않는 기능이다.
* 시스템 사양에 **필요한 메서드만 정의**하도록 하자.

## 문제 퇴치 효과
* 데이터 클래스에 숨어 있던 문제를 잘 퇴지했는가?

|퇴치한 문제|이유|
|---|---|
|중복 코드|필요한 로직이 Money 클래스의 내부에 모여 있으므로, 다른 클래스에 중복 코드가 작성될 일이 줄어듦|
|수정 누락|중복 코드가 발생하지 않으므로, 수정 시 누락될 일이 줄어듦|
|가독성 저하|필요한 로직이 모두 Money 클래스 내부에 모여 있어, 디버깅 또는 기능 변경 시 관련된 로직을 찾는데 시간을 많이 할애하지 않아도 되므로 가독성이 높아짐|
|쓰레기 객체|생성자에서 인스턴스 변수 값을 확정하므로, 초기화되지 않은 상태가 있을 수 없음|
|잘못된 값|가드를 적용하고, 인스턴스 변수에 final 수시자를 붙여 불변으로 만들었으므로, 잘못된 값이 들어오지 않음|
|생각하지 못한 부수 효과|final 수식자를 붙여 불변 변수로 만들었으므로, 부수 효과로부터 안전|
|값 전달 실수|매개변수를 Money 클래스 타입으로 받도록 하여, 다른 자료형의 값을 실수로 넣었을 때 컴파일 오류 발생함|

* 이처럼 인스턴스 변수를 중심으로, 인스턴스 변수가 잘못된 상태에 빠지지 않게 설계하면 문제를 퇴치할 수 있다.
* **클래스 설계**란, **인스턴스 변수가 잘못된 상태에 빠지지 않게 하기 위한 구조를 만드는 것**

## 프로그램 구조의 문제 해결에 도움을 주는 디자인 패턴
* 프로그램 구조를 개선하는 설계 방법을 디자인 패턴이라고 한다.

|디자인 패턴|효과|
|---|---|
|완전 생성자|잘못된 상태로부터 보호|
|값 객체|특정한 값과 관련된 로직의 응집도를 높임|
|전략|조건 분기를 줄이고, 로직을 단순화함|
|정책|조건 분기를 단순화하고, 더 자유롭게 만듦|
|일급 컬렉션|값 객체의 일종, 컬렉션과 관련된 로직의 응집도를 높임|
|스프라우트 클래스(Sprout Class)|기존 로직을 변경하지 않고, 안전하게 새로운 기능을 추가함|

* 위 Money 클래스는 **완전 생성자**와 **값 객체**라는 두 가지 패턴을 적용한 것

### 완전 생성자
* 잘못된 상태로부터 클래스를 보호하기 위한 디자인 패턴
  * 인스턴스 변수를 모두 초기화해야만 객체를 생성할 수 있게, 매개변수를 가진 생성자를 만듬
  * 생성자 내부에는 가드를 사용해 잘못된 값이 들어오지 못하게 만듦
    * 위 Money 클래스의 생성자

### 값 객체
* 값을 클래스(자료형)로 나타내는 디자인 패턴
  * 예를 들어 금액을 단순히 기본형 (int 자료형 등) 지역 변수 또는 매개변수로 사용하면, 금액 계산 로직이 이곳저곳 분산될 수 있음
  * 또한, 실수로 의미가 다른 값들끼리 섞일 수도 있음
  * 이러한 문제를 막기 위해 값을 클래스로 정의한다.
    * 위 Money 클래스에서의 `add()` 메서드는 매개변수로 Money 자료형만 받을 수 있다.
