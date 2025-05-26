---
title: Special Case Pattern을 활용한 레거시 리팩토링
date: 2022-12-10
modified: 2022-12-10
tags: [swe]
description: Special Case Pattern을 활용한 레거시 리팩토링
image: ""
---

## 링크

- 영상 : [백명석님의 클린 코더스 Function Structure Part.2](https://www.youtube.com/watch?v=cgiDv1XFWsk&ab_channel=%EB%B0%B1%EB%AA%85%EC%84%9D)
- 저장소 : [msbaek/stack-example/extract-interface](https://github.com/msbaek/stack-example/tree/extract-interface)

## 상황

- 만약, capacity를 0으로 stack을 생성하면 어떻게 되는가? 새로운 exception을 추가해야 하는가?
- 아니다. 우리는 너무 많은 exception을 생성하는 것은 별로 원하지 않는다.
- 게다가 capacity 가 0인 스택이 오류인지 의미가 있는 건지 알아야 한다.

우리가 또한 알아야 하는 것은 사이즈가 0인 스택은 잘 정의된 행위를 가진다는 것이다.

- push를 호출하면 Overflow
- pop을 호출하면 Underflow
- getSize는 0을 반환

이런 상황을 막기 위해서, 스택 클래스의 모든 메서드에 size가 0인지 비교하는 로직을 추가할 수있다. 하지만 그것보다는 `null object pattern(special case pattern)`을 사용할 수
있다.

아이디어는 null이나 capacity가 0인 것과 같은 special case를 잘 포장하는 것이다. 간단한 아이디어지만, 대부분 메서드에 size가 0인지 확인하는 로직을
제거 가능할 정도로 강력한 패턴이다. **즉, 이 패턴을 활용하면 특이 케이스를 확인하는 코드 대부분을 단순한 함수 호출로 바꿀 수 있다.**

## 용어에 대해서

null object와 유사하긴 하지만, 좀 더 특수화된 객체라고 봐야하는지 헷갈린다.

아래는 [How to Reduce Cyclomatic Complexity Part 2: Special Case Pattern](https://www.codinghelmet.com/articles/reduce-cyclomatic-complexity-special-case)
에서 인용했다.

> Special Case design pattern is an extension of the Null Object pattern idea

종종 혼용해서 사용하시는 분들도 계시고, 일단 내 생각에는 결국 유사한 아이디어를 사용하는 것이기 때문에 혼용해서 써도 무방하다는 입장이다. (나중에 제대로 찾아봐야겠다..)
그리고 명석님도 null object pattern(special case pattern)라고 표현하셨다.

## 개선 방법

> LegacyStack -> Stack으로 이름 변경 후 개선 진행

### stack 인터페이스 추출

1. 기존 레거시에서 인터페이스 추출
2. 원본 클래스의 이름 변경 및 가능한 경우 인터페이스 사용 체크
3. 기존 Stack을 BoundedStack으로 변경
4. 인터페이스 이름 Stack으로 유지
5. 추출 메서드 선택 후 리팩토링

### zero stack 생성

1. BoundedStack의 make 함수에서 capacity가 0인 경우 익명 클래스 반환
2. 구현한 익명 클래스를 내부 클래스로 추출 (익명을 내부로 변환 F6)
3. 내부를 상위 클래스로 이동
