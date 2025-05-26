---
title: 객체지향 생활 체조
date: 2022-11-22
modified: 2022-11-22
tags: [swe]
description: 객체지향 생활 체조
image: ""
---

> 소트웍스 앤솔러지의 객체지향 생활 체조 파트를 정리한 내용입니다. 문제가 될 경우 삭제조치 하도록 하겠습니다.

### 객체지향 생활 체조 규칙이란

소트웍스 앤솔러지에서 나오는 객체지향 생활 체조 규칙이란 간단히 말하자면, 객체지향적인 코드를 유도하는 9가지 규칙을 의미한다.

> 좋은 설계의 바탕에 있는 핵심 개념은 쉽게 알 수 있다. 예를 들어 보통 중요하다고 알고 있는 7가지 코드 품질 항목, 응집력(cohesion), 느슨한 결합(loose coupling), 무중복(zero duplication), 캡슐화(encapsulation), 테스트 가능성(testability), 가독성(readability), 초점(focus)이 있다. 하지만 그런 개념을 실제로 펼치기란 어렵다. 캡슐화가 데이터, 구현, 타입, 설계, 또는 생성의 은닉을 가리킨다고 이해하는 것과 캡슐화를 잘 구현하는 코드를 설계하는 것은 전혀 별개의 문제다. 그래서 좋은 객체지향 설계의 원칙을 자기 것으로 하고 실생활에서 쓰도록 도와주는 훈련을 소개하려 한다.

 객체지향이 되기 위해 대체로 필요한 코드 작성을 유도하기 위한 9가지 ‘경험 법칙’은 아래와 같다. 

> 아래 경험 규칙을 따르다 보면, 자연스럽게 객체지향적인 코드를 작성할 수 있다.

1.  한 메서드에 오직 한 단계의 들여 쓰기만 한다.
2.  else 예약어(keyword)를 쓰지 않는다.
3.  모든 원시 값과 문자열을 포장(wrap)한다.
4.  한 줄에 점을 하나만 찍는다.
5.  줄여 쓰지 않는다(축약 금지).
6.  모든 엔티티(entity)를 작게 유지한다.
7.  2개 이상의 인스턴스 변수를 가진 클래스를 쓰지 않는다.
8.  일급 콜렉션을 쓴다.
9.  getter/setter/property를 쓰지 않는다.

### 규칙 1 : 메서드당 들여 쓰기 한번

#### 규칙을 적용하는 이유

-   거대한 메서드는 응집력이 떨어진다.
-   어떤 지침은 메서드 길이를 5줄로 제한하는 것이지만, 기존에 메서드의 길이가 500줄이라면 의욕이 꺾이기 쉽다.
-   메서드의 길이를 줄이는 대신, 각 메서드가 정확히 한 가지 일을 하는지, 즉 메서드당 하나의 제어 구조나 하나의 문장 단락(block)으로 되어 있는지를 지키려고 노력한다.
-   한 메서드 안에 중첩된 제어구조가 있다면 다단계의 추상화를 코드로 짠 것이며, 고로 한 가지 이상의 일을 하고 있다는 뜻이다.

#### 규칙의 이점

-   애플리케이션의 각 단위가 더 작아짐에 따라 재사용의 수준이 증가한다.
-   100줄로 구현된 5가지 작업을 맡은 하나의 메서드 안에서 재사용의 가능성을 골라내기란 어려울 수 있다. 주어진 콘텍스트에서 단일 객체의 상태를 관리하는 3줄짜리 메서드라면 많은 다양한 콘텍스트에서 쓸 수 있다.
-   각 메서드는 구현을 이름에 맞추다 보니 평이해진다. 이렇게 적어진 코드에서 버그의 존재를 판별하기 쉽다.

#### 나의 생각

설득력있는 규칙이라고 생각했다. 우아한테크코스 미션 요구사항에서도 indent를 줄이라고 제한한다. 단 궁금한 것은 왜 거대한 메서드가 응집력이 떨어지는지 직관적으로 떠올리기 힘들다. 직관적으로 봤을 때는 액체 괴물이 여러 개가 합쳐지면, 응집력이 높은 것이 아닌가? 나는 다음과 같이 결론을 내렸다.

> 메서드가 크다는 것은,  여러 일을 하고 있다는 신호일 수도 있다. 그렇다면, 분산 되어야 할 각각의 여러 일들이  적절한 메서드에 응집되어 있지 않고 한 곳에 모여있는 것이다. 즉, 응집도가 떨어진다. 

#### 규칙 적용

아래와 같이 메서드 추출(Extract Method) 기능을 써서 메서드에 들여쓰기가 1단계만 남을 때까지 동작 코드를 뽑아낸다.

```java
class Board {
   ...
   String board() {
      StringBuffer buf = new StringBuffer();
      for (int i = 0; i < 10; i++) {
         for (int j = 0; j < 10; j++)
            buf.append(data[i][j]);
         buf.append("\n");
      }
      return buf.toString();
   }
}
 
class Board {
   ...
   String board() {
      StringBuffer buf = new StringBuffer();
      collectRows(buf);
      return buf.toString();
   }
 
   void collectRows(StringBuffer buf) {
      for (int i = 0; i < 10; i++)
         collectRow(buf, i);
   }
 
   void collectRow(StringBuffer buf, int row) {
      for (int i = 0; i < 10; i++)
         buf.append(data[row][i]);
      buf.append("\n");
   }
}
```

### 규칙 2 : else 예약어 금지

#### 규칙을 적용하는 이유

-   모든 프로그래머는 if/else 구문을 이해한다. 이 구문은 거의 모든 프로그래밍 언어에 들어 있고, 간단한 조건 논리는 누구나 쉽게 이해한다. 거의 모든 프로그래머는 따라가기 불가능할 정도로 지저분한 중첩 조건문이나 몇 페이지씩 되는 case 문을 본 적이 있을 것이다. (코드가 지저분해진다.)
-   더욱이 리팩토링보다는 그냥 기존 조건문에 분기를 하나 더 치기가 무척 쉽다는 점이다. 조건문은 또한 곧잘 코드 중복의 원흉이기도 하다. (분기를 치기 쉬워 중복 코드의 원흉이 될 수 있다.)

#### 규칙의 이점

-   가독성이 좋아진다. (코드가 간결해짐)
-   중복 코드의 원흉을 없앰 
-   유지 보수하기 좋아진다.

#### 나의 생각

분기를 계속해서 만들게 되면, 코드를 보기 어려울 뿐만 아니라 내가 지금 뭘 만들고 있는지 까먹게 한다. 또한 개인적인 경험상 설계를 신경 쓰지 않으면 else 금지 규칙을 지키기 어려웠던 것 같다. 이번 프리코스 미션을 진행하면서 else 금지 제약을 지키기 위해서 생각하고 개발하는 연습을 했었다.

#### 규칙 적용

객체지향 언어는 다형성이라는 강력한 도구를 통해 복잡한 조건문을 처리할 수 있다. 간단한 경우라면 else를 보호절(guard clause)과 조기 반환(early return)으로 대체 가능하다. 단, 너무 많이 하면 간결함을 저해하기 쉽다는 점을 간과해서는 안 된다. 

> 다형성을 채택한 설계는 읽고 유지하기 쉬우며 더욱 분명히 코드의 의도를 표현하기에 이른다.

디자인 패턴 책의 Strategy 패턴을 보면 상태 인라인(status inline)에 분기를 막기 위해 다형성(polymorphism)을 쓰는 예제가 나온다. 상태에 대한 분기가 몇 군데 걸쳐 중복돼 있을 때 Strategy 패턴은 특히 유용하다. 또한 특정 상황에서 Null Object Pattern을 시도하면 도움이 된다.  (다형성을 이용하는 것을 유도하는 것일까?)

```java
public static void endMe() {
   if (status == DONE) {
      doSomething();
   } else {
      // another code
   }
}

public static void endMe() {
   if (status == DONE) {
      doSomething();
      return ;
   }
   // another code
}
 
public static Node head() {
   if (isAdvancing()) { return first; }
   else { return last; }
}
 
public static Node head() {
   return isAdvancing() ? first : last;
}
```

### 규칙 3 : 원시값과 문자열의 포장

> int 값 하나 자체는 그냥 아무 의미 없는 스칼라 값일 뿐이다. 어떤 메서드가 int 값을 매개변수로 받는다면 그 메서드 이름은 해당 매개변수의 의도를 나타내기 위해 모든 수단과 방법을 가리지 않아야 한다.

#### 규칙을 적용하는 이유

-   원시값들은 값의 정의만 가지고, 다른 의미를 가지지 못한다.

#### 적용의 이점

-   값이 값 이상의 의미를 가진다면, 프로그래머에게 의미를 전달할 수 있다. 즉, 이 값을 왜 사용하는지에 대한 정보를 표현할 수 있다.
-   또한 시간이나 돈과 같은 작은 객체는 행위를 놓을 분명한 곳을 마련해 줘서, 그렇지 않았다면 다른 클래스의 주위를 겉돌았을지도 모르는 사태를 방지한다. 

#### 나의 생각

무슨 말인지 직관적이지 않아서 어렵다. 따라서 추가 학습을 진행했다. [림딩동님의 3번 규칙 티스토리 글](https://limdingdong.tistory.com/9)에서 우연찮게 새로운 발견을 할 수 있었다. 이번 4주 차 미션에서는 validator를 클래스로 분리했다. 하지만 그렇게 되면 상태를 갖는 객체가 스스로 책임을 지지 못하는 문제가 생긴다. 간과했던 것이다..  규칙 적용 방식은 림딩동님의 글에서 가져왔다. (댓글에 누군가 이미 퍼가도 괜찮냐고 물어봤는데, 출처만 남기면 괜찮다고 하셨다.)

#### 규칙 적용

>  int, long, String과 같은 원시 타입, 문자열 변수를 객체로 포장해 사용하라는 지침이다. 프로그래밍에서 변수는 '상태'로 쓰일 수 있다. 상태는 '자료'가 아니라 '정보'다. 단순히 값을 나타내는 것뿐 아니라, 비즈니스적인 의미를 함께 표현해준다. 이렇게 업무적 의미를 갖는 변수를 객체로 포장해 사용하면 얻는 이점이 많다.  
>   
> [출처 : 림딩동님 티스토리 \[객체지향 생활체조 원칙\] 규칙 3. 모든 원시값과 문자열을 포장한다](https://limdingdong.tistory.com/9)

아래는 신용점수를 기반으로 고객을 심사해주는 역할을 하는데, score 검증의 중복이 생기는 문제가 있다. 따로 validator를 만들어 검증할 수 있지만, 스스로 자신의 상태를 관리하지 못하는 모양이 된다.

```java
public class EvaluateService {

    private static final int MIN_CREDIT_SCORE = 0;
    private static final int MAX_CREDIT_SCORE = 1000;

    public void evaluateCustomerCreditRate(int score) {

        if (score < MIN_CREDIT_SCORE || score > MAX_CREDIT_SCORE) {
            throw new IllegalArgumentException("신용점수는 " + MIN_CREDIT_SCORE + " 점 이상, " + MAX_CREDIT_SCORE + " 점 미만이어야 합니다.");
        }

        // 검증로직...
    }
}
```

3번 규칙을 적용하면 아래와 같이 score를 CreditScore로 포장할 수 있다. 이렇게 되면, EvaludateService를 사용하는 프로그램에서는 CreditScore 클래스의 인스턴스를 생성해야 한다. (자연스럽게 유효성 검사 가능) 또한 다른 곳에서 신용점수라는 정보가 사용돼도 검증 로직을 일관되게 관리 가능하다.

```java
public class CreditScore {

    private static final int MIN_CREDIT_SCORE = 0;
    private static final int MAX_CREDIT_SCORE = 1000;

    private int value;

    public CreditScore(int value) {
        if (value < MIN_CREDIT_SCORE || value > MAX_CREDIT_SCORE) {
            throw new IllegalArgumentException("신용점수는 " + MIN_CREDIT_SCORE + " 점 이상, " + MAX_CREDIT_SCORE + " 점 미만이어야 합니다.");
        }
        this.value = value;
    }
}

public class EvaluateService {

    public void evaluateCustomerCreditRate(CreditScore creditScore) {
        
        // 검증로직
    }
}
```

또한 원시 타입을 포장함으로 메서드의 시그니처가 명확해질 수 있다. 

```java
public void evaluateCustomerCreditRate(int score)
public void evaluateCustomerCreditRate(CreditScore creditScore)
```

### 규칙 4 : 한 줄에 한 점만 사용

> 종종 하나의 동작에 대해 어떤 객체가 맡고 있는지 구분하기 어려울 때가 있다. 여러 개의 점이 들어 있는 코드 몇 줄을 들여다보기 시작하면 책임 소재의 오류를 많이 발견하기 시작한다. 어떠한 코드 한 줄에서라도 점이 하나 이상 있으면 그른 곳에서 동작이 일어나고 있다는 뜻이다.

#### 규칙을 적용하는 이유

-   어쩌면 객체는 다른 두 객체를 동시에 다루고 있을지도 모른다.  이 경우 그 객체는 중개상, 즉 너무 많은 사람들에 대해 지나치게 알고 있는 꼴이다(마치 유통에 있어 중개상을 배제하고 직거래하듯). 
-   중복된 점들은 캡슐화를 어기고 있다는 방증이다.

#### 규칙의 이점

-   코드의 가독성을 높여준다.
-   객체 간의 불필요한 결합도를 낮춰준다. (객체가 알아야 하는 다른 객체의 수를 최소화시킬 수 있다.)
-   종속성을 최소화시킨다. 

#### 나의 생각

> 객체가 자기 속을 들여다보려 하기보다는 뭔가 작업을 하도록 만들어야 한다. 캡슐화의 주 요점은 클래스 경계를 벗어나 알 필요가 없는 타입으로 진입하지 않는 것이다.

A->B->C 구조에서 A.B.C.doSomething를 해버리게 된다면, B->C 사이의 연관관계가 무안해지게 될 것이다. C가 변경되게 되면, A와 B 둘 다 변경해야 한다. 이것이 강결합의 단점이라고 생각했다. 캡슐화를 어겼다는 것은 B의 C가 숨겨져야 하는 데, 개방되어 A가 C랑 강결합할 수 있는 여지를 준 것이라고 생각해봤다.

#### 규칙 적용

> 디미터(Demeter)의 법칙(“친구 하고만 대화하라”)이 좋은 출발점이긴 하지만, 이런 식으로 생각하자. 자기 소유의 장난감, 자기가 만든 장난감, 그리고 누군가 자기에게 준 장난감하고만 놀 수 있다. 하지만 절대 장난감의 장난감과 놀면 안 된다.

```java
class Board {
   ...
 
   class Piece {
      ...
      String representation;
   }
   class Location {
      ...
      Piece current;
   }
 
   String boardRepresentation() {
      StringBuffer buf = new StringBuffer();
      for (Location l : squares())
         buf.append(l.current.representation.substring(0, 1));
      return buf.toString();
   }
}
 
class Board {
   ...
 
   class Piece {
      ...
      private String representation;
      String character() {
         return representation.substring(0, 1);
      }
 
      void addTo(StringBuffer buf) {
         buf.append(character());
      }
   }
   class Location {
      ...
      private Piece current;
 
      void addTo(StringBuffer buf) {
         current.addTo(buf);
      }
   }
 
   String boardRepresentation() {
      StringBuffer buf = new StringBuffer();
      for (Location l : squares())
         l.addTo(buf);
      return buf.toString();
   }
}
```

### 규칙 5 : 축약 금지

> 누구나 실은 클래스, 메서드, 또는 변수의 이름을 줄이려는 유혹에 곧잘 빠지곤 한다. 그런 유혹을 뿌리쳐라. 축약은 혼란을 야기하며, 더 큰 문제를 숨기는 경향이 있다.

#### 규칙을 적용하는 이유

-   축약을 하고 싶은 이유는 계속 반복해서 똑같은 단어를 치기 때문일 수 있다. 만일 그 경우라면 아마도 메서드가 너무 대대적으로 사용되어 중복을 없앨 기회를 놓치고 있는 것이다. 
-   메서드 이름이 길어지고 있기 때문이라 생각이 든다면, 책임 소재의 오류나 클래스의 부재라는 신호일 수 있다.

#### 규칙의 이점

-   이름을 줄이지 않는다면 의미 전달을 명확히 하여, 혼돈을 방지한다. (cnt, idx, ..등등)
-   또한 Order.shipOrder을 문맥이 중복되는 이름을 자제하여, 간결한 호출 표현이 가능하다. (Order.ship)

#### 나의 생각

이 규칙을 적용하기 위해서는 클래스와 메서드를 적절히 잘 나누는 연습을 해야겠다고 느꼈다.(당연한 이야기겠지만..!) Order.isUser처럼 이상한 메서드가 있으면, 중복 문맥을 제거해도, 의미를 잘 전달해도 혼돈을 야기한다.

#### 규칙 적용

클래스와 메서드 이름을 한두 단어로 유지하려고 노력하고 문맥을 중복하는 이름을 자제하자.  이 훈련을 하기 위해서는 모든 엔티티가 한두 단어로 된 이름을 축약 없이 가져야 한다. 예를 들어, 클래스 이름이 Order라면 shipOrder라고 메서드 이름을 지을 필요가 없다. 짧게 ship()이라고 하면 클라이언트에서는 order.ship()라고 호출하며, 간결한 호출의 표현이 된다.

### 규칙 6 :  모든 엔티티를 작게 유지

> 이 말은 50줄 이상 되는 클래스와 파일이 10개 이상인 패키지는 없어야 한다는 뜻이다.

#### 규칙을 적용하는 이유

-   50줄 이상의 클래스는 보통 한 가지 일 이상을 하는 것이며, 따라서 코드의 이해와 재사용을 점점 더 어렵게 끌고 간다.

#### 규칙의 이점

-   클래스가 점점 작아지고 하는 일이 줄어들며 패키지 크기를 제한함에 따라 패키지가 하나의 목적을 달성하기 위해 모인 연관 클래스들의 집합을 나타낸다는 사실을 알아차리게 된다. (패키지도 클래스처럼 응집력 있고 단일한 목표가 있어야 한다.)
-   패키지를 작게 유지하면 패키지 자체가 진정한 정체성을 지니게 된다.
-   클래스를 작게 작성할 때 난감한 경우는 같이 있어야 말이 되는 동작의 묶음이 있을 때다. 이는 패키지를 최대한 활용해야 하는 곳이기도 하다. (패키지의 활용도를 높임)
-   50줄짜리 클래스는 스크롤하지 않고도 한 화면에 볼 수 있다는 부가적인 혜택도 있으며 한눈에 파악하기도 쉽다.

#### 나의 생각

클래스가 한 가지 일만 하고 있다면, 50줄 이상이어도 되는지 의문이 든다. 만약 클래스가 한가지 일만 한다면, 50줄 이상이어도 괜찮지 않을까란 생각을 했다.

#### 규칙 적용

 규칙은 간단하다. 50줄 이상 되는 클래스와 파일이 10개 이상인 패키지는 없어야 한다.

### 규칙 7 : 2개 이상의 인스턴스 변수를 가진 클래스 사용 금지

> 클래스는 두 종류, 즉 인스턴스 변수 한 개의 상태를 유지하는 종류와 두 개의 독립된 변수를 조율하는 종류가 있음을 파악하게 된다. 일반적으로 그 두 종류의 책임을 혼용하면 안 된다.

#### 규칙을 적용하는 이유

-   대부분의 클래스가 간단하게 하나의 상태 변수를 처리하는 일을 맡아 마땅하지만 몇몇 경우 둘이 필요할 때가 있다. 새로운 인스턴스 변수를 하나 더 기존 클래스에 추가하면 클래스의 응집도는 즉시 떨어진다. 
-   클래스의 인스턴수 변수는 클래스가 관리하는 상태이다. 클래스의 상태는 클래스의 정체성을 나타낸다. 따라서 여러 상태의 종류를 가지게 되면, 클래스가 여러 정체성을 가지고 있다는 것이다. 

#### 규칙의 이점

-   객체의 복잡도를 낮춘다.
-   버그 발생 가능성 낮춘다.

#### 나의 생각

많이 반성하게 되는 규칙이다. 기존의 나를 되돌아보면, 인스턴스 변수를 그저 편의를 위한 용도로 사용했던 것 같다. 객체의 상태와 정체성을 인스턴스 변수가 표현하게 될 텐데, 만약 그저 편의 용도로만 사용했다면 객체지향과 부합하지 않는 것 같다.

#### 규칙 적용

> 자주, 인스턴스 변수의 분해는 여러 개의 관련 인스턴스 변수의 공통성을 이해하게 해 준다. 때때로 여러 관련 인스턴스 변수가 실은 일급 콜렉션(first-class collection) 안에서 연관된 삶을 살고 있다.

```java
 class Name {
   String first;
   String middle;
   String last;
}

// 다음과 같이 분해
class Name {
   Surname family;
   GivenNames given;
}
 
class Surname {
   String family;
}
 
class GivenNames {
   List<String> names;
}
```

### 규칙 8 : 일급 콜렉션 사용

>  콜렉션을 감싸고 이외에 다른 필드를 가지고 있지 않는 클래스를 일급 콜렉션이라고 한다. 

#### 규칙을 적용하는 이유

-    지역변수로 있는 콜렉션에 대해서 어떠한 처리를 해줘야 할 때 이 지역변수를 가진 클래스가 그걸 처리하기 위해서 기능을 하나 갖기보다는 애초에 그 지역변수가 차라리 일급 콜렉션으로 만들고, 일급 콜렉션의 책임으로 분산해주면 더 객체지향적인 코드가 된다.

#### 규칙의 이점

-   상태와 행위를 한 곳에서 관리할 수 있어서 응집도가 높아진다.
-   비즈니스에 종속적인 자료구조를 만들 수 있다. (비즈니스적인 제약사항을 둘 수 있음)

#### 나의 생각

이번 4주 차 다리 게임을 구현할 때, bridge를 List<String>으로 표현했다. 하지만, 이를 일급 콜렉션으로 만들어 사용했다면, 다리를 생성하거나, 제약을 걸 때 더욱 편리했을 것이란 생각했다.

#### 규칙 적용

이 규칙의 적용은 간단하다. 콜렉션을 포함한 클래스는 반드시 다른 멤버 변수가 없어야 한다. 각 콜렉션은 그 자체로 포장돼 있으므로 이제 콜렉션과 관련된 동작은 근거지가 마련된 셈이다.

```java
class Bridge {
  private final List<String> bridge;
  ...
}
```

### 규칙 9 : getter/setter/property 사용 금지

> 이 규칙은 흔히 “Tell, Don't Ask”라고 일컫는다.

#### 규칙을 적용하는 이유

-   느슨한 결합과 유연한 협력에 도움을 주기 위한 성질은 캡슐화인데, getter와 setter는 캡슐화를 방해한다.

#### 규칙의 이점

-   객체지향 프로그래밍의 핵심 개념 중 캡슐화를 지킬 수 있다.
-   즉, 객체에 메시지를 보내고 메시지를 받은 객체 스스로 상태에 대한 처리 로직을 수행하도록 한다.

#### 나의 생각

이론적으로는 이해했지만, 막상 실제 코드에 작성하려고 하면 어려운 규칙인 것 같다. 이번 4주 차 공통 피드백에서도 비슷한 피드백을 받기도 했다.

#### 규칙 적용

아래는 로또 게임의 예시이다. LottoGame에서 getter를 이용해서, 직접 Lotto의 상태를 판단하게 된다. 여기에 규칙을 적용하면 Lotto 클래스에 contain 메서드를 만들어서 외부에서 메시지를 요청하도록 하여  캡슐화를 만족할 수 있다.

```java
public class Lotto {
    private final List<Integer> numbers;
    
    public Lotto(List<Integer> numbers) {
        this.numbers = numbers;
    }

    public int getNumbers() {
        return numbers;
    }
}

public class LottoGame {
    public void play() {
        Lotto lotto = new Lotto(...);

        // Tell, Don't ask 위반
        lotto.getNumbers().contains(number);
        
        // Tell, Don't ask 위반
        lotto.getNumbers().stream()...
    }
```

### 느낀 점

아직은 객체지향 탐구에 대해서 갈 길이 멀다고 느꼈다. 좋은 내용임에도 불구하고 흡수력이 낮았다. 나중에 경력과 실력을 쌓은 다음에 다시 복습해보면, 더욱 이 내용에 대해서 깊은 의미를 느낄 수 있을 것 같다.