# Chapter02. 설계 첫걸음
## 요약 정리
* 이름은 의도를 알 수 있도록 짓는다.
* 목적별로 변수를 따로 만들어 사용한다.
* 의미 있는 로직을 모아 메서드로 만든다.
  * 메서드 이름을 의미 있는 이름으로 지으면 무슨 일을 하는 메서드인지 알기 쉽다.
* 유지 보수와 변경이 쉽도록 이름과 로직을 신경써서 작성하는 것이 곧 설계이다.

## 의도를 분명히 전달할 수 있는 이름 설계하기
* 다음 예제 코드를 보면, 어떤 로직인지 알 수 있는가?
```java
int d = 0;
d = p1 + p2;
d = d - ((d1 + d2) / 2);
if (d < 0) [
    d = 0;
]
```
* 무엇을 계산하는 건지 전혀 이해할 수 없다.
* 이처럼 이름을 짧게 줄이면 다른 사람이 코드를 보거나, 장본인이 시간이 지난 후 다시 보려고 할 때 이해하기가 매우 어렵다.
* **의도를 알 수 있는 이름을 사용하도록 한다.**
* 자주 바뀔 가능성이 있는 코드를 구현할 때는 **변수 이름을 쉽게 붙이는 것**도 훌륭함

## 목적별로 변수를 따로 만들어 사용하기
* 변수에 값을 다시 할당하는 것을 **재할당**이라고 함
  * 재할당은 **변수의 용도가 바뀌는 문제**를 일으키기 쉽다.
    * 버그 발생 가능성
* 재할당으로 기존의 변수를 다시 사용하지 말고, **목적별로 변수를 만들어 사용**하자
* 기존
```java
int damageAmount = 0;
damageAmount = playerStrengthPower + playerWeaponPower;
damageAmount = damageAmount - ((enemyBodyDefence + enemyArmorDefence) / 2);
```

* 목적별로 변수 만들기
```java
int totalPlayerAttackPower = playerStrengthPower + playerWeaponPower;
int totalEnemyDefence = enemyBodyDefence + enemyArmorDefence;

int damageAmount = totalPlayerAttackPower - (totalEnemyDefence / 2);
```

## 의미 있는 것들을 모아 메서드로 만들기
* 계산 로직들이 단순 나열되어 있으면, 로직이 어디에서 시작해서 어디에서 끝나는지, 무슨 일을 하는지 알기 어렵다.
  * 계산 로직이 복잡해지면 실수로 결과가 달라질 수도 있음
* **의미 있는 로직을 모아서 메서드로 구현**하는 것이 좋다.
```java
int sumPlayerAttackPower(int playerStrengthPower, int playerWeaponPower) {
    return playerStrengthPower + playerWeaponPower;
}

int sumEnemyDefence(int enemyBodyDefence, int enemyArmorDefence) {
    return enemyBodyDefence + enemyArmorDefence;
}
```

* 서로 다른 계산 작업을 각각의 메서드로 분리하여 쉽게 구분 가능하다.
<br/>

**유지 보수와 변경이 쉽도록 변수의 이름과 로직을 신경 써서 작성하는 것이 곧 설계이다.** <br/>
