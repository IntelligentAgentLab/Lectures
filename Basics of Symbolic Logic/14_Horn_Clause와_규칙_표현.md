# 14장. Horn Clause와 규칙 표현

## 1. 이 장의 목표

13장에서는 변수가 있는 FOL 식을 ground instance로 만들고, ground atom을 명제변수로 대응하여 SAT 문제로 바꾸는 과정을 배웠다. 이번 장에서는 일반적인 절 가운데 계산에 유리한 형태인 **Horn clause**를 선택하고, 그것을 사실과 규칙으로 읽는 방법을 배운다.

이 장을 마치면 다음을 할 수 있다.

1. 리터럴과 절의 개념을 다시 설명할 수 있다.
2. Horn clause를 “긍정 리터럴이 최대 하나인 절”로 판정할 수 있다.
3. Horn clause와 definite clause를 구분할 수 있다.
4. 사실, 규칙, 목표, 제약의 형식을 분류할 수 있다.
5. 절 형식과 조건문 형식을 서로 변환할 수 있다.
6. 규칙의 머리와 몸체를 구분할 수 있다.
7. 변수가 있는 Horn 규칙의 전칭 양화 해석을 설명할 수 있다.
8. Horn clause가 FOL의 제한된 부분 형식임을 설명할 수 있다.
9. Horn 표현의 장점과 표현력의 제한을 예로 들 수 있다.
10. 유한한 명제 Horn 이론에 전방 추론을 실행할 수 있다.
11. 전방 추론과 단위 전파의 대응을 설명할 수 있다.
12. Horn-SAT가 일반 SAT보다 다루기 쉬운 이유를 설명할 수 있다.

### 핵심 질문

- 어떤 절이 Horn clause인가?
- 왜 부정 리터럴이 많아도 Horn clause일 수 있는가?
- definite clause는 Horn clause와 어떻게 다른가?
- 하나의 절을 어떻게 “조건이 성립하면 결론이 성립한다”는 규칙으로 읽는가?
- 사실, 규칙, 목표, 제약은 논리적으로 어떤 모양을 갖는가?
- 전방 추론은 어떻게 새로운 사실을 만들어 내는가?
- Horn-SAT의 효율성은 어디까지 보장되는가?

---

## 2. 일반 CNF에서 실행 가능한 규칙으로

명제논리의 CNF는 절들의 논리곱이다.

$
C_1\land C_2\land\cdots\land C_m
$

각 절은 리터럴들의 논리합이다.

$
L_1\lor L_2\lor\cdots\lor L_n
$

일반 절은 여러 개의 긍정 리터럴을 포함할 수 있다.

$
\neg P\lor Q\lor R
$

이를 조건문처럼 읽으면 다음과 같다.

$
P\rightarrow Q\lor R
$

결론이 하나로 정해지지 않는다. $P$가 참일 때 $Q$와 $R$ 중 적어도 하나가 참이어야 하지만, 어느 것을 새 사실로 추가해야 하는지는 절 하나만으로 결정할 수 없다.

Horn clause는 결론 후보를 최대 하나로 제한한다.

$
\neg B_1\lor\cdots\lor\neg B_n\lor A
$

이 절은 다음 규칙으로 읽힌다.

$
B_1\land\cdots\land B_n\rightarrow A
$

몸체가 모두 참이면 머리 $A$를 참으로 만들면 된다. 이 단순한 방향성이 실행 가능한 추론 절차의 기초가 된다.

---

## 3. 리터럴과 절 복습

### 3.1 긍정 리터럴

원자식 자체를 긍정 리터럴이라고 한다.

$
P,quad Student(alice),quad Parent(x,y)
$

### 3.2 부정 리터럴

원자식에 부정이 붙은 표현을 부정 리터럴이라고 한다.

$
\neg P,quad \neg Student(alice),quad \neg Parent(x,y)
$

### 3.3 절

리터럴들을 논리합으로 연결한 식을 절이라고 한다.

$
\neg P\lor\neg Q\lor R
$

리터럴 하나도 절로 본다. 이를 **단위 절**이라고 한다.

$
P
$

빈 절은 리터럴이 하나도 없는 절이며 항상 거짓을 나타낸다.

$
\square
$

또는

$
\bot
$

으로 표기한다.

---

## 4. Horn clause의 정의

**Horn clause**는 긍정 리터럴을 **최대 하나** 포함하는 절이다.

“최대 하나”에는 두 경우가 포함된다.

1. 긍정 리터럴이 정확히 하나인 절
2. 긍정 리터럴이 하나도 없는 절

### 예시

다음은 모두 Horn clause다.

$
P
$

$
\neg P\lor Q
$

$
\neg P\lor\neg Q\lor R
$

$
\neg P\lor\neg Q
$

마지막 절에는 긍정 리터럴이 없지만, 여전히 “최대 하나”라는 조건을 만족한다.

다음은 Horn clause가 아니다.

$
P\lor Q
$

$
\neg P\lor Q\lor R
$

각 절에 긍정 리터럴이 두 개 있기 때문이다.

> Horn 여부는 전체 리터럴 수가 아니라 긍정 리터럴 수로 결정한다.

---

## 5. Horn 판정 절차

절 하나가 주어졌을 때 다음 순서로 판단한다.

1. 절을 리터럴 단위로 나눈다.
2. 각 리터럴에서 가장 바깥의 부정 기호를 확인한다.
3. 부정되지 않은 원자식의 수를 센다.
4. 그 수가 0 또는 1이면 Horn, 2 이상이면 non-Horn이다.

### 예시 1

$
\neg A\lor\neg B\lor C
$

- 부정 리터럴: $\neg A$, $\neg B$
- 긍정 리터럴: $C$
- 긍정 리터럴 수: 1
- 판정: Horn clause

### 예시 2

$
\neg A\lor\neg B\lor\neg C
$

- 긍정 리터럴 수: 0
- 판정: Horn clause

### 예시 3

$
A\lor\neg B\lor C
$

- 긍정 리터럴: $A$, $C$
- 긍정 리터럴 수: 2
- 판정: Horn clause가 아님

---

## 6. Definite clause

**Definite clause**는 긍정 리터럴이 **정확히 하나**인 Horn clause다.

일반형은 다음과 같다.

$
\neg B_1\lor\cdots\lor\neg B_n\lor A
$

$A$가 유일한 긍정 리터럴이다.

조건문 형식으로 바꾸면 다음과 같다.

$
B_1\land\cdots\land B_n\rightarrow A
$

여기서

- $A$: 규칙의 **머리** 또는 결론
- $B_1,\ldots,B_n$: 규칙의 **몸체** 또는 조건

이다.

### 포함 관계

$
\text{definite clause}\subsetneq\text{Horn clause}
$

모든 definite clause는 Horn clause지만, 긍정 리터럴이 없는 Horn clause는 definite clause가 아니다.

---

## 7. 사실도 definite clause다

몸체가 비어 있고 긍정 리터럴 하나만 있는 절을 **사실**이라고 읽는다.

$
A
$

이를 조건문 형식으로 쓰면 다음과 같이 볼 수 있다.

$
\top\rightarrow A
$

조건 $\top$은 언제나 참이므로 $A$는 바로 성립한다.

### 예시

$
Parent(alice,bob)
$

$
Student(mina)
$

사실은 긍정 리터럴이 정확히 하나이므로 definite clause이자 Horn clause다.

---

## 8. 규칙

몸체에 하나 이상의 원자가 있고 머리에 긍정 원자 하나가 있는 definite clause를 보통 **규칙**이라고 읽는다.

$
B_1\land\cdots\land B_n\rightarrow A
$

### 예시

$
Parent(x,y)\land Parent(y,z)\rightarrow Grandparent(x,z)
$

절 형식은 다음과 같다.

$
\neg Parent(x,y)\lor\neg Parent(y,z)\lor Grandparent(x,z)
$

이 절에는 긍정 리터럴 $Grandparent(x,z)$가 하나뿐이다.

---

## 9. 목표 절과 부정 절

긍정 리터럴이 하나도 없는 Horn clause를 **부정 절** 또는 **goal clause**라고 부른다.

일반형은 다음과 같다.

$
\neg B_1\lor\cdots\lor\neg B_n
$

이는 다음과 동치다.

$
\neg(B_1\land\cdots\land B_n)
$

또는

$
B_1\land\cdots\land B_n\rightarrow\bot
$

### 예시

$
\neg Human(x)\lor\neg Robot(x)
$

$
Human(x)\land Robot(x)\rightarrow\bot
$

이는 어떤 개체가 동시에 인간이면서 로봇인 경우를 허용하지 않는다는 제약으로 읽을 수 있다.

---

## 10. 목표와 제약은 모양이 같고 역할이 다르다

다음 형식은 목표와 제약에 모두 사용된다.

$
B_1\land\cdots\land B_n\rightarrow\bot
$

### 목표로 사용할 때

질의 $Q$가 지식베이스에서 따라오는지 보이기 위해 $\neg Q$를 추가하고 모순을 찾을 수 있다.

$
K\models Q
\quad\Longleftrightarrow\quad
K\land\neg Q\text{가 만족 불가능}
$

이때 $\neg Q$는 증명을 유도하는 목표 절의 역할을 한다.

### 제약으로 사용할 때

특정 조합이 동시에 참이 되는 것을 금지한다.

$
Bird(x)\land Mammal(x)\rightarrow\bot
$

같은 논리적 모양이라도 사용 맥락에 따라 “증명할 목표의 부정” 또는 “금지 조건”으로 읽힐 수 있다.

---

## 11. 사실·규칙·목표·제약 비교

| 종류 | 절 형식 | 규칙 형식 | 긍정 리터럴 수 |
|---|---|---|---:|
| 사실 | $A$ | $\top\rightarrow A$ | 1 |
| 규칙 | $\neg B_1\lor\cdots\lor\neg B_n\lor A$ | $B_1\land\cdots\land B_n\rightarrow A$ | 1 |
| 목표 | $\neg B_1\lor\cdots\lor\neg B_n$ | $B_1\land\cdots\land B_n\rightarrow\bot$ | 0 |
| 제약 | $\neg B_1\lor\cdots\lor\neg B_n$ | $B_1\land\cdots\land B_n\rightarrow\bot$ | 0 |

사실과 규칙은 definite clause다. 목표와 제약은 Horn clause지만 definite clause는 아니다.

---

## 12. 절 형식에서 조건문 형식으로

다음 definite clause를 보자.

$
\neg B_1\lor\cdots\lor\neg B_n\lor A
$

먼저 부정 리터럴을 한 묶음으로 본다.

$
\neg(B_1\land\cdots\land B_n)\lor A
$

조건문의 동치

$
X\rightarrow Y\equiv\neg X\lor Y
$

를 적용하면 다음을 얻는다.

$
B_1\land\cdots\land B_n\rightarrow A
$

### 구체적 예시

$
\neg Rain\lor\neg Cold\lor Snow
$

$
Rain\land Cold\rightarrow Snow
$

부정되어 있던 원자들은 몸체의 긍정 조건으로 이동하고, 유일한 긍정 리터럴은 머리가 된다.

---

## 13. 조건문 형식에서 절 형식으로

다음 규칙에서 시작한다.

$
B_1\land\cdots\land B_n\rightarrow A
$

조건문을 제거한다.

$
\neg(B_1\land\cdots\land B_n)\lor A
$

드모르간 법칙을 적용한다.

$
\neg B_1\lor\cdots\lor\neg B_n\lor A
$

### 구체적 예시

$
Parent(x,y)\land Parent(y,z)\rightarrow Grandparent(x,z)
$

$
\neg Parent(x,y)\lor\neg Parent(y,z)\lor Grandparent(x,z)
$

이 변환은 논리적 동치를 보존한다.

---

## 14. 머리와 몸체

Horn 규칙을 다음처럼 쓴다.

$
A\leftarrow B_1,\ldots,B_n
$

또는

$
A\leftarrow B_1\land\cdots\land B_n
$

화살표 오른쪽의 조건들이 성립하면 왼쪽의 결론을 얻는다는 뜻이다.

### 머리

$
A
$

- 규칙이 도출하는 결론
- definite clause의 유일한 긍정 리터럴

### 몸체

$
B_1,\ldots,B_n
$

- 머리를 도출하기 위해 모두 필요한 조건
- 절 형식에서는 부정 리터럴로 나타남

### 읽기 방향 주의

$
A\leftarrow B
$

는

$
B\rightarrow A
$

를 뜻한다. 화살표가 왼쪽을 향하므로 자연어 읽기 방향과 혼동하지 않도록 한다.

---

## 15. 하나의 예시를 세 표기로 쓰기

### FOL 조건문 표기

$
\forall x\forall y\forall z
\bigl(
Parent(x,y)\land Parent(y,z)
\rightarrow Grandparent(x,z)
\bigr)
$

### 절 표기

$
\neg Parent(x,y)\lor\neg Parent(y,z)\lor Grandparent(x,z)
$

### 논리 프로그램식 표기

```prolog
grandparent(X, Z) :-
    parent(X, Y),
    parent(Y, Z).
```

세 표현은 같은 규칙 구조를 보여 주지만 강조점이 다르다.

- FOL 표기: 양화와 논리적 의미
- 절 표기: CNF와 해소법
- 프로그램식 표기: 머리, 몸체, 실행 방향

---

## 16. 변수는 암묵적으로 전칭 양화된다

Horn 규칙에 자유롭게 나타난 변수는 보통 규칙 전체에서 전칭 양화된 것으로 해석한다.

$
Parent(x,y)\land Parent(y,z)\rightarrow Grandparent(x,z)
$

의 전칭 폐쇄는 다음과 같다.

$
\forall x\forall y\forall z
\bigl(
Parent(x,y)\land Parent(y,z)
\rightarrow Grandparent(x,z)
\bigr)
$

이는 특정 세 개체에 대한 규칙이 아니라 모든 가능한 $x,y,z$에 대한 일반 규칙이다.

### 주의

변수를 대문자로 쓰는 것은 Prolog 계열의 표기 관습이다. FOL에서 변수인지 상항인지는 글자 모양 자체가 아니라 언어의 문법이 결정한다.

---

## 17. 변수가 있는 규칙과 grounding

다음 규칙을 보자.

$
Parent(x,y)\land Parent(y,z)\rightarrow Grandparent(x,z)
$

치환

$
\theta={x/alice,;y/bob,;z/charlie}
$

를 적용하면 ground rule을 얻는다.

$
Parent(alice,bob)\land Parent(bob,charlie)
\rightarrow Grandparent(alice,charlie)
$

절 형식은 다음과 같다.

$
\neg Parent(alice,bob)
\lor\neg Parent(bob,charlie)
\lor Grandparent(alice,charlie)
$

grounding은 Horn 성질을 보존한다. 원래 절의 유일한 긍정 리터럴이 ground instance에서도 유일한 긍정 리터럴로 남기 때문이다.

---

## 18. FOL, Horn clause, definite clause의 관계

Horn clause는 FOL과 별개의 논리 체계가 아니다. FOL에서 허용되는 절 가운데 형태를 제한한 부분이다.

$
\mathrm{FOL}
\supset
\mathrm{Horn\ clauses}
\supset
\mathrm{definite\ clauses}
$

### FOL

- 임의의 연결사와 양화사 조합을 표현할 수 있다.
- 한 절에 여러 긍정 리터럴이 나타날 수 있다.

### Horn clause

- 절마다 긍정 리터럴을 최대 하나만 허용한다.
- definite clause와 부정 절을 모두 포함한다.

### Definite clause

- 절마다 긍정 리터럴이 정확히 하나다.
- 사실과 결론이 하나인 규칙으로 읽을 수 있다.

포함 관계는 표현 가능한 문장의 집합에 대한 관계다. 추론 목적과 사용 방식은 문맥에 따라 달라질 수 있다.

---

## 19. Horn clause가 표현하기 어려운 것

Horn clause는 모든 FOL 문장을 직접 표현하지 못한다.

### 선택적 결론

$
P\rightarrow Q\lor R
$

절 형식은

$
\neg P\lor Q\lor R
$

이고 긍정 리터럴이 두 개이므로 Horn이 아니다.

### 원자 수준의 부정 결론

$
Bird(x)\rightarrow\neg Flies(x)
$

절 형식은

$
\neg Bird(x)\lor\neg Flies(x)
$

이다. 이것은 긍정 리터럴이 없는 Horn clause이며 제약으로는 표현할 수 있다. 그러나 definite 프로그램에서 $\neg Flies(x)$를 새로운 긍정 사실처럼 도출하는 규칙은 아니다.

### 존재 결론

$
Person(x)\rightarrow\exists y,Parent(y,x)
$

몸체가 참일 때 새로운 존재 증인을 만들어야 한다. 단순한 definite clause 표기만으로는 이 구조를 그대로 나타내기 어렵다.

### 정확히 하나의 선택

“$Q$와 $R$ 중 정확히 하나”와 같은 배타적 선택은 여러 Horn 규칙과 추가 제약만으로도 일부 상황에서 기술할 수 있지만, 일반적인 선택적 결론을 한 개의 Horn 절로 직접 표현할 수는 없다.

---

## 20. 제한이 주는 이점

Horn 형식은 표현력을 줄이는 대신 추론 구조를 단순하게 만든다.

1. 규칙의 결론이 최대 하나이므로 도출 방향이 명확하다.
2. 몸체의 모든 원자가 참이 되는 순간 머리를 추가할 수 있다.
3. 사실 집합을 반복적으로 확장하는 전방 추론이 가능하다.
4. 유한 명제 Horn 이론에는 유일한 최소 모델이 있다.
5. Horn-SAT는 입력 크기에 선형인 알고리즘으로 풀 수 있다.
6. 규칙을 사람이 읽기 쉬운 형태로 표현할 수 있다.

> 표현력의 제한은 단순한 손실이 아니라 계산 가능한 추론 구조를 얻기 위한 설계 선택이다.

---

## 21. Horn 지식베이스의 구성

다음과 같은 지식베이스를 생각하자.

### 사실

$
Parent(alice,bob)
$

$
Parent(bob,charlie)
$

$
Parent(charlie,dana)
$

### 규칙

$
Parent(x,y)\land Parent(y,z)\rightarrow Grandparent(x,z)
$

$
Grandparent(x,z)\rightarrow Ancestor(x,z)
$

$
Parent(x,y)\rightarrow Ancestor(x,y)
$

이 지식베이스는 사실에서 시작해 규칙을 적용하면서 새로운 관계를 도출할 수 있다.

---

## 22. 전방 추론의 기본 아이디어

**전방 추론** 또는 **forward chaining**은 알려진 사실에서 출발해 적용 가능한 규칙의 결론을 반복적으로 추가하는 방법이다.

### 기본 절차

1. 현재 참으로 알려진 원자 집합 $S$를 만든다.
2. 몸체의 모든 원자가 $S$에 포함된 규칙을 찾는다.
3. 그 규칙의 머리를 $S$에 추가한다.
4. 더 이상 새로운 원자가 추가되지 않을 때까지 반복한다.

### 핵심 성질

- 한 번 참으로 도출된 원자는 제거하지 않는다.
- 사실 집합은 단조롭게 커진다.
- 유한한 ground atom 집합에서는 반드시 고정점에 도달한다.

---

## 23. 전방 추론 예제

다음 명제 Horn 지식베이스를 보자.

### 사실

$
P,quad Q
$

### 규칙

$
P\land Q\rightarrow R
$

$
R\rightarrow S
$

$
S\land Q\rightarrow T
$

### 0단계

$
S_0=\{P,Q\}
$

### 1단계

$P$와 $Q$가 모두 있으므로 $R$을 추가한다.

$
S_1=\{P,Q,R\}
$

### 2단계

$R$이 있으므로 $S$를 추가한다.

$
S_2=\{P,Q,R,S\}
$

### 3단계

$S$와 $Q$가 있으므로 $T$를 추가한다.

$
S_3=\{P,Q,R,S,T\}
$

이제 새 사실이 없으므로 고정점에 도달했다.

---

## 24. 규칙 순서는 결론을 바꾸지 않는다

단순한 구현에서는 규칙을 위에서 아래로 훑는다. 어떤 순서로 훑느냐에 따라 중간 단계의 모습은 달라질 수 있다.

그러나 다음 조건에서는 최종 폐쇄 집합이 같다.

- 유한한 명제 definite 규칙 집합
- 모든 적용 가능한 규칙을 빠짐없이 반복
- 도출한 사실을 삭제하지 않음

즉, 공정하게 반복하면 같은 최소 고정점에 도달한다.

한 번만 규칙 목록을 훑고 멈추면 규칙 순서에 따라 필요한 결론을 놓칠 수 있다. “새 사실이 없을 때까지” 반복하는 것이 중요하다.

---

## 25. 고정점

전방 추론을 연산자 $T_K$로 나타낼 수 있다. $S$가 현재 사실 집합일 때,

$
T_K(S)
=
S\cup
\{A\mid (B_1\land\cdots\land B_n\rightarrow A)\in K,
\{B_1,\ldots,B_n\}\subseteq S\}
$

이다.

초기 사실 집합 $S_0$에서 반복한다.

$
S_0,quad T_K(S_0),quad T_K^2(S_0),\ldots
$

어떤 단계에서

$
T_K(S_i)=S_i
$

가 되면 더 이상 새 사실이 나오지 않는다. 이 $S_i$가 고정점이다.

유한한 ground atom 집합에서는 각 원자가 최대 한 번 새로 추가되므로 반복은 끝난다.

---

## 26. 최소 모델 직관

definite Horn 지식베이스는 모든 사실과 규칙을 참으로 만드는 모델 가운데 참인 원자가 가장 적은 모델을 갖는다. 이를 **최소 모델**이라고 한다.

전방 추론으로 얻은 폐쇄 집합은 바로 이 최소 모델의 참인 원자 집합과 일치한다.

### 예시

$
K=\{P,;P\rightarrow Q\}
$

전방 추론은 $P$에서 $Q$를 얻는다.

$
M_{min}=\{P,Q\}
$

$R$까지 참으로 둔 해석도 $K$의 모델일 수 있지만, $R$은 규칙에서 요구되지 않는다.

$
M'=\{P,Q,R\}
$

$M'$는 모델이지만 최소 모델은 아니다.

### 중요한 구분

최소 모델에서 거짓인 원자가 고전논리적으로 부정되었다는 뜻은 아니다. “도출되지 않음”과 “부정이 증명됨”은 구분해야 한다.

---

## 27. 제약 검사

definite 규칙으로 폐쇄 집합을 만든 뒤 부정 Horn 절을 검사할 수 있다.

### 지식베이스

$
P
$

$
P\rightarrow Q
$

### 제약

$
P\land Q\rightarrow\bot
$

전방 추론으로 $Q$가 도출된다. 따라서 제약의 몸체 $P\land Q$가 모두 참이 되고 $\bot$에 도달한다.

이 경우 지식베이스 전체는 만족 불가능하다.

### 제약이 발화하지 않는 경우

$
P\land R\rightarrow\bot
$

$R$이 도출되지 않는다면 이 제약은 위반되지 않는다.

---

## 28. 단위 전파와의 연결

다음 Horn 절을 보자.

$
\neg P\lor\neg Q\lor R
$

사실 $P$와 $Q$가 참이라고 하자. 절에서 거짓이 된 부정 리터럴을 제거하면

$
R
$

만 남는다. 따라서 $R$은 참이어야 한다.

이것이 SAT 관점의 **단위 전파**다.

규칙 관점에서는

$
P\land Q\rightarrow R
$

의 몸체가 모두 참이 되었으므로 머리 $R$을 도출한다.

따라서 definite Horn 절에서

$
\text{전방 추론}
\quad\leftrightarrow\quad
\text{단위 전파}
$

는 같은 핵심 연산을 서로 다른 표기로 본 것이다.

---

## 29. Horn-SAT

모든 절이 Horn clause인 CNF 식의 만족 가능성 문제를 **Horn-SAT**라고 한다.

### 예시

$
P
\land
(\neg P\lor Q)
\land
(\neg Q\lor R)
\land
(\neg R\lor\neg S)
$

각 절의 긍정 리터럴 수는 다음과 같다.

| 절 | 긍정 리터럴 수 |
|---|---:|
| $P$ | 1 |
| $\neg P\lor Q$ | 1 |
| $\neg Q\lor R$ | 1 |
| $\neg R\lor\neg S$ | 0 |

모든 절이 Horn이므로 Horn-SAT 문제다.

---

## 30. Horn-SAT 판정 알고리즘

유한한 명제 Horn 이론을 다음 두 부분으로 나눈다.

1. 사실과 definite 규칙
2. 부정 절 또는 제약

### 알고리즘

1. 모든 긍정 단위 절을 참 집합 $S$에 넣는다.
2. 몸체의 모든 원자가 $S$에 들어온 definite 규칙의 머리를 $S$에 넣는다.
3. 새 원자가 없을 때까지 2단계를 반복한다.
4. 몸체의 모든 원자가 $S$에 포함된 제약이 있는지 확인한다.
5. 그런 제약이 있으면 UNSAT, 없으면 SAT다.

### SAT일 때의 모델

도출된 원자들은 참, 도출되지 않은 원자들은 거짓으로 두면 Horn 이론의 최소 모델을 얻는다.

---

## 31. Horn-SAT 예제: SAT

다음 지식베이스를 보자.

$
P
$

$
P\rightarrow Q
$

$
Q\rightarrow R
$

$
R\land S\rightarrow\bot
$

### 전방 추론

$
\{P\}
ightarrow\{P,Q\}\rightarrow\{P,Q,R\}
$

$S$는 도출되지 않는다. 따라서 제약 $R\land S\rightarrow\bot$은 발화하지 않는다.

한 만족 모델은 다음과 같다.

$
P=\top,quad Q=\top,quad R=\top,quad S=\bot
$

결론은 SAT다.

---

## 32. Horn-SAT 예제: UNSAT

다음 지식베이스를 보자.

$
P
$

$
P\rightarrow Q
$

$
Q\rightarrow R
$

$
P\land R\rightarrow\bot
$

전방 추론은 다음과 같이 진행된다.

$
\{P\}
ightarrow\{P,Q\}\rightarrow\{P,Q,R\}
$

이제 제약의 몸체 $P\land R$이 모두 참이다.

$
\bot
$

에 도달하므로 UNSAT다.

---

## 33. 왜 일반 SAT보다 쉬운가

일반 SAT에서는 다음 절처럼 여러 긍정 리터럴이 있을 수 있다.

$
\neg P\lor Q\lor R
$

$P$가 참이어도 $Q$와 $R$ 가운데 어느 것을 참으로 선택할지 분기해야 한다.

Horn 절에서는 긍정 리터럴이 최대 하나다.

$
\neg P\lor\neg Q\lor R
$

$P$와 $Q$가 참이면 선택 없이 $R$을 참으로 정한다.

### 핵심 차이

| 일반 SAT | Horn-SAT |
|---|---|
| 여러 긍정 결론 후보 가능 | 긍정 결론 후보 최대 하나 |
| 탐색과 분기가 필요할 수 있음 | 단조로운 사실 확장으로 판정 |
| SAT는 NP-완전 | Horn-SAT는 선형시간에 해결 가능 |

효율성의 근원은 “어떤 결론을 선택할 것인가”라는 조합적 분기가 사라지는 데 있다.

---

## 34. 선형시간 구현의 직관

규칙마다 아직 충족되지 않은 몸체 원자의 수를 센다고 하자.

$
count(r)=|body(r)|
$

어떤 원자 $P$가 새로 참이 되면 $P$를 몸체에 포함하는 규칙들의 카운트만 1씩 줄인다.

카운트가 0이 되면 그 규칙의 머리를 큐에 넣는다.

### 자료 구조

- 참이 된 원자를 저장하는 집합
- 새 원자를 처리하는 큐
- 각 원자에서 그것을 몸체에 포함한 규칙로 가는 역색인
- 규칙별 남은 조건 수

각 리터럴 출현을 상수 번 정도만 처리하므로 전체 입력 크기에 비례하는 시간으로 구현할 수 있다.

$
O(\text{절과 리터럴의 전체 크기})
$

---

## 35. “Horn은 쉽다”의 정확한 범위

Horn-SAT의 선형시간 결과는 **유한한 명제 Horn 이론**에 대한 것이다.

다음 상황까지 자동으로 쉬워지는 것은 아니다.

### 변수가 있는 FOL Horn 규칙

변수를 ground term으로 치환해야 할 수 있다. ground term이 많으면 grounding 폭발이 일어난다.

### 함수 기호가 있는 경우

$
a,;f(a),;f(f(a)),\ldots
$

처럼 ground term이 무한히 생길 수 있다. 전방 추론이 유한 단계에 끝나지 않을 수 있다.

### 무한 영역에 대한 일반 FOL 추론

명제 Horn-SAT 알고리즘의 복잡도 보장을 그대로 적용할 수 없다.

> Horn의 형태적 제한과 유한한 명제화 가능성은 서로 다른 조건이다.

---

## 36. Datalog이 유한성을 확보하는 방식

Datalog은 대체로 다음과 같은 제한을 둔다.

- 양의 항수를 가진 함수 기호를 사용하지 않는다.
- 프로그램과 데이터에 나타난 상항이 유한하다.
- 규칙은 definite Horn 형태를 중심으로 한다.
- 머리에 나타나는 변수는 몸체에서 적절히 제한된다.

함수 기호가 없으면 새로운 중첩 함수항이 계속 생성되지 않는다. 유한한 상항과 술어로 만들 수 있는 ground atom 수가 유한하므로 전방 추론이 고정점에 도달한다.

이 절은 다음 장의 논리 프로그래밍을 위한 연결 고리다. 구체적인 문법과 질의 실행은 15장에서 다룬다.

---

## 37. 닫힌 세계 가정과 혼동하지 않기

전방 추론에서 $P$가 도출되지 않았다고 하자.

고전 FOL의 관점에서는 이것만으로 $\neg P$를 결론 내릴 수 없다.

$
K\not\vdash P
\quad\not\Rightarrow\quad
K\vdash\neg P
$

일부 논리 프로그래밍 시스템은 **실패에 의한 부정** 또는 닫힌 세계 가정을 사용해 도출되지 않은 것을 거짓처럼 처리한다. 그러나 이것은 Horn clause의 정의 자체에서 자동으로 따라오는 원리가 아니다.

### 구분

- Horn clause: 절의 문법적 형태 제한
- 최소 모델: definite 이론의 의미론적 성질
- 닫힌 세계 가정: 알려지지 않은 사실을 거짓으로 취급하는 추가 가정
- 실패에 의한 부정: 질의 실패를 부정 성공으로 해석하는 운영 규칙

---

## 38. 자주 발생하는 오류

### 오류 1. 부정 리터럴이 있으면 Horn이 아니라고 생각한다

Horn 판정은 부정 리터럴의 수가 아니라 긍정 리터럴의 수를 센다.

### 오류 2. 긍정 리터럴이 없는 절을 Horn에서 제외한다

Horn은 긍정 리터럴이 최대 하나인 절이다. 0개도 포함한다.

### 오류 3. Horn clause와 definite clause를 같은 말로 사용한다

definite clause는 긍정 리터럴이 정확히 하나다. 부정 절은 Horn이지만 definite가 아니다.

### 오류 4. 몸체의 논리곱을 논리합으로 바꾼다

$
\neg P\lor\neg Q\lor R
$

는

$
P\land Q\rightarrow R
$

이지 $P\lor Q\rightarrow R$가 아니다.

### 오류 5. $A\leftarrow B$를 $A\rightarrow B$로 읽는다

왼쪽 화살표 표기에서는 $B$가 조건이고 $A$가 결론이다.

### 오류 6. 한 번의 규칙 스캔으로 전방 추론을 끝낸다

새 사실이 다른 규칙을 활성화할 수 있으므로 고정점까지 반복해야 한다.

### 오류 7. 도출되지 않은 원자를 고전적 부정으로 결론 낸다

정보 부족과 부정 증명은 다르다.

### 오류 8. FOL Horn 규칙도 항상 선형시간에 풀린다고 말한다

선형시간 보장은 유한한 명제 Horn-SAT에 대한 것이다.

---

## 39. 문제 해결 체크리스트

### 절을 분류할 때

1. 식이 실제로 하나의 절인지 확인한다.
2. 리터럴을 분리한다.
3. 긍정 리터럴 수를 센다.
4. 0개면 negative Horn clause, 1개면 definite clause, 2개 이상이면 non-Horn이다.

### 규칙으로 변환할 때

1. 유일한 긍정 리터럴을 머리로 둔다.
2. 부정 리터럴의 원자들을 긍정형으로 바꾸어 몸체에 둔다.
3. 몸체는 논리곱으로 묶는다.
4. 변수가 있으면 전칭 폐쇄를 확인한다.

### 전방 추론을 할 때

1. 초기 사실 집합을 적는다.
2. 모든 몸체 조건이 충족된 규칙을 찾는다.
3. 새로운 머리만 추가한다.
4. 새 사실이 없을 때까지 반복한다.
5. 마지막에 모든 제약을 검사한다.

---

## 40. 수업 활동

### 활동 1. Horn 신호등

교사가 절을 제시하면 학생은 다음 색으로 분류한다.

- 초록: definite clause
- 노랑: 긍정 리터럴이 없는 Horn clause
- 빨강: non-Horn clause

판정 뒤에는 긍정 리터럴 수를 말하게 한다.

### 활동 2. 표기 번역 릴레이

한 조는 절 형식을 조건문으로, 다른 조는 조건문을 절 형식으로 바꾼다. 이후 머리와 몸체를 표시하고 서로 검토한다.

### 활동 3. 인간 규칙 엔진

학생에게 사실 카드와 규칙 카드를 나누어 준다. 몸체 카드가 모두 모이면 머리 카드를 사실 영역에 추가한다. 더 이상 추가할 카드가 없을 때 종료한다.

### 활동 4. 제약 경보

전방 추론으로 사실을 확장하는 동안 어떤 제약의 몸체가 모두 참이 되는 순간 경보를 울린다. SAT와 UNSAT을 구분한다.

### 활동 5. 표현력 경계 찾기

다음 문장들이 한 개의 definite Horn 규칙으로 직접 표현 가능한지 토론한다.

- 모든 학생은 학습자다.
- 비가 오면 우산을 쓰거나 실내에 머문다.
- 모든 사람에게 부모가 존재한다.
- 어떤 개체도 인간이면서 로봇일 수 없다.

---

## 41. 연습문제

### 기본

1. $\neg P\lor Q$는 Horn clause인가?
2. $P\lor Q$는 Horn clause인가?
3. $\neg P\lor\neg Q$는 Horn clause인가? definite clause인가?
4. $R$은 어떤 종류의 Horn clause인가?
5. $\neg A\lor\neg B\lor C$에서 머리와 몸체를 찾으라.
6. $A\land B\rightarrow C$를 절 형식으로 바꾸라.
7. $\neg A\lor\neg B\lor\neg C$를 $\bot$을 사용한 규칙으로 바꾸라.
8. $H\leftarrow P,Q$를 오른쪽 화살표 조건문으로 바꾸라.

### 분류와 변환

9. 다음 절들을 definite, negative Horn, non-Horn으로 분류하라.

   $
   A,quad \neg A\lor B,quad \neg A\lor\neg B,quad A\lor B,quad
   \neg A\lor B\lor C
   $

10. 다음 자연어를 Horn 규칙으로 나타내라. “어떤 것이 새이고 건강하면 날 수 있다.”
11. 다음 규칙을 절 형식으로 바꾸라.

   $
   Parent(x,y)\land Parent(y,z)\rightarrow Ancestor(x,z)
   $

12. 다음 절을 조건문으로 바꾸라.

   $
   \neg Student(x)\lor\neg Studies(x)\lor Passes(x)
   $

13. 문제 12의 규칙에서 암묵적으로 전칭 양화된 변수를 모두 쓰라.
14. $P\rightarrow Q\lor R$가 한 개의 Horn clause가 아닌 이유를 설명하라.

### 전방 추론

15. 사실 $P,Q$와 규칙 $P\land Q\rightarrow R$, $R\rightarrow S$가 있다. 고정점을 구하라.
16. 사실 $A$와 규칙 $A\rightarrow B$, $B\rightarrow C$, $C\rightarrow D$가 있다. $D$가 도출되는 단계를 적으라.
17. 문제 16에 제약 $A\land D\rightarrow\bot$을 추가하면 SAT인가?
18. 사실 $P$와 규칙 $Q\rightarrow R$만 있을 때 $R$이 도출되지 않는 이유를 설명하라.
19. 사실 $P$와 규칙 $P\rightarrow Q$, $P\rightarrow R$, $Q\land R\rightarrow S$의 최소 모델을 구하라.
20. 다음 절에서 사실 $P,Q$가 주어졌을 때 단위 전파 결과를 구하라.

   $
   \neg P\lor\neg Q\lor R
   $

### 개념과 한계

21. 모든 definite clause가 Horn clause인 이유를 설명하라.
22. 모든 Horn clause가 definite clause라는 주장이 틀린 이유를 예로 들라.
23. 전방 추론과 단위 전파가 어떻게 대응하는지 설명하라.
24. Horn-SAT에서 조합적 분기가 줄어드는 이유를 설명하라.
25. 함수 기호가 있는 FOL Horn 규칙에 선형시간 Horn-SAT 결과를 그대로 적용할 수 없는 이유를 설명하라.
26. “도출되지 않음”과 “부정이 증명됨”의 차이를 설명하라.
27. Datalog에서 함수 기호를 제한하는 이유를 grounding 관점에서 설명하라.
28. 다음 지식베이스가 SAT인지 전방 추론으로 판정하라.

   $
   P,quad P\rightarrow Q,quad Q\land R\rightarrow S,quad S\rightarrow\bot
   $

---

## 42. 연습문제 해설

### 문제 1

긍정 리터럴 $Q$가 하나이므로 Horn이자 definite clause다.

### 문제 2

긍정 리터럴이 $P,Q$ 두 개이므로 Horn이 아니다.

### 문제 3

긍정 리터럴이 없으므로 Horn clause지만 definite clause는 아니다.

### 문제 4

긍정 단위 절이며 몸체가 빈 definite clause, 즉 사실이다.

### 문제 5

머리는 $C$, 몸체는 $A\land B$다.

### 문제 6

$
\neg A\lor\neg B\lor C
$

### 문제 7

$
A\land B\land C\rightarrow\bot
$

### 문제 8

$
P\land Q\rightarrow H
$

### 문제 9

- $A$: definite
- $\neg A\lor B$: definite
- $\neg A\lor\neg B$: negative Horn
- $A\lor B$: non-Horn
- $\neg A\lor B\lor C$: non-Horn

### 문제 10

$
Bird(x)\land Healthy(x)\rightarrow Flies(x)
$

변수의 전칭 폐쇄를 명시하면 $\forall x$를 앞에 붙인다.

### 문제 11

$
\neg Parent(x,y)\lor\neg Parent(y,z)\lor Ancestor(x,z)
$

### 문제 12

$
Student(x)\land Studies(x)\rightarrow Passes(x)
$

### 문제 13

$
\forall x\bigl(Student(x)\land Studies(x)\rightarrow Passes(x)\bigr)
$

### 문제 14

절 형식 $\neg P\lor Q\lor R$에 긍정 리터럴 $Q,R$가 두 개 있기 때문이다.

### 문제 15

$
\{P,Q\}\rightarrow\{P,Q,R\}\rightarrow\{P,Q,R,S\}
$

### 문제 16

0단계 $\{A\}$, 1단계 $\{A,B\}$, 2단계 $\{A,B,C\}$, 3단계 $\{A,B,C,D\}$다.

### 문제 17

$A$와 $D$가 모두 도출되어 제약이 발화하므로 UNSAT다.

### 문제 18

$Q$가 사실 집합에 없으므로 $Q\rightarrow R$의 몸체가 충족되지 않는다.

### 문제 19

$
\{P,Q,R,S\}
$

### 문제 20

$P,Q$가 참이면 $\neg P,\neg Q$가 거짓이므로 단위 리터럴 $R$이 남아 $R$을 참으로 둔다.

### 문제 21

definite clause는 긍정 리터럴이 정확히 하나이므로 긍정 리터럴이 최대 하나라는 Horn 조건을 항상 만족한다.

### 문제 22

$\neg P\lor\neg Q$는 긍정 리터럴이 0개라 Horn이지만 definite는 아니다.

### 문제 23

$B_1\land\cdots\land B_n\rightarrow A$에서 몸체가 모두 참이면 전방 추론은 $A$를 도출한다. 대응하는 절에서 부정 몸체 리터럴들이 모두 거짓이 되면 단위 리터럴 $A$가 남는다.

### 문제 24

각 Horn 절에 긍정 결론 후보가 최대 하나이므로 여러 결론 후보 중 하나를 선택하는 분기가 없다.

### 문제 25

함수 기호는 무한히 많은 ground term을 생성할 수 있다. 그러면 유한한 명제 Horn-SAT 입력 자체가 만들어지지 않거나 전방 추론이 끝나지 않을 수 있다.

### 문제 26

$P$를 도출하지 못한 것은 $P$에 대한 정보가 부족하다는 뜻일 수 있다. $\neg P$를 결론 내리려면 별도의 부정 정보나 추가 의미론이 필요하다.

### 문제 27

함수 기호를 제한하면 중첩 함수항의 무한 생성을 막아 가능한 ground atom 집합을 유한하게 유지하기 쉽다.

### 문제 28

$P$에서 $Q$는 도출되지만 $R$이 없으므로 $S$는 도출되지 않는다. 제약 $S\rightarrow\bot$도 발화하지 않으므로 SAT다. 최소 모델은 $\{P,Q\}$다.

---

## 43. 60분 수업 운영안

### 0–7분: CNF에서 규칙으로

- 일반 절의 여러 긍정 결론이 만드는 분기를 보여 준다.
- Horn 절이 결론 후보를 최대 하나로 제한한다는 동기를 제시한다.

### 7–17분: Horn과 definite 분류

- 긍정·부정 리터럴을 복습한다.
- 사실, 규칙, 부정 절을 분류한다.
- “최대 하나”와 “정확히 하나”를 비교한다.

### 17–28분: 절과 규칙의 변환

- $\neg B_1\lor\cdots\lor\neg B_n\lor A$를 조건문으로 바꾼다.
- 머리와 몸체를 표시한다.
- 변수의 전칭 폐쇄와 grounding을 연결한다.

### 28–40분: 전방 추론

- 사실 집합에서 적용 가능한 규칙을 찾는다.
- 새 사실이 없을 때까지 반복한다.
- 고정점과 최소 모델의 직관을 설명한다.

### 40–50분: 제약과 Horn-SAT

- 제약이 발화하면 UNSAT임을 보인다.
- 같은 과정을 단위 전파로 다시 읽는다.

### 50–56분: 효율성과 경계

- 일반 SAT의 분기와 Horn-SAT의 단조로운 전파를 비교한다.
- 선형시간 보장이 유한 명제 Horn-SAT에 한정됨을 강조한다.

### 56–60분: 형성평가

- Horn 판정, 규칙 변환, 전방 추론, 적용 범위를 확인한다.

### 수업에서 강조할 문장

> Horn clause는 결론 후보를 최대 하나로 제한하여, 탐색을 사실의 단조로운 확장으로 바꾼다.

---

## 44. 형성평가

### 문항 1

$\neg P\lor\neg Q\lor R$가 Horn clause인 이유는 무엇인가?

**정답:** 유일한 긍정 리터럴이 $R$ 하나이기 때문이다.

### 문항 2

Horn clause와 definite clause의 차이는 무엇인가?

**정답:** Horn clause는 긍정 리터럴을 최대 하나 포함하고, definite clause는 정확히 하나 포함한다.

### 문항 3

$\neg A\lor\neg B\lor C$를 규칙 형식으로 바꾸라.

**정답:** $A\land B\rightarrow C$다.

### 문항 4

사실 $P$와 규칙 $P\rightarrow Q$, $Q\rightarrow R$가 있을 때 전방 추론의 고정점은 무엇인가?

**정답:** $\{P,Q,R\}$다.

### 문항 5

Horn-SAT가 선형시간에 풀린다는 말을 함수 기호가 있는 모든 FOL Horn 이론에 그대로 적용할 수 있는가?

**정답:** 없다. 선형시간 보장은 유한한 명제 Horn-SAT 입력에 대한 것이며, 함수 기호는 무한 grounding과 비종료를 일으킬 수 있다.

---

## 45. 핵심 정리

1. Horn clause는 긍정 리터럴을 최대 하나 포함하는 절이다.
2. 긍정 리터럴이 정확히 하나인 Horn clause가 definite clause다.
3. 긍정 단위 절은 몸체가 빈 definite clause이며 사실로 읽힌다.
4. $\neg B_1\lor\cdots\lor\neg B_n\lor A$는 $B_1\land\cdots\land B_n\rightarrow A$와 동치다.
5. 유일한 긍정 리터럴 $A$는 머리, 부정 리터럴에 대응하는 원자들은 몸체다.
6. 긍정 리터럴이 없는 Horn clause는 목표 또는 제약으로 사용할 수 있다.
7. 변수가 있는 Horn 규칙은 보통 모든 변수를 전칭 양화한 것으로 해석한다.
8. grounding은 Horn 성질을 보존한다.
9. Horn clause는 FOL의 제한된 부분이며, definite clause는 Horn clause의 더 제한된 부분이다.
10. 여러 긍정 결론이 필요한 규칙은 한 개의 Horn 절로 직접 표현할 수 없다.
11. 전방 추론은 몸체가 충족된 규칙의 머리를 반복적으로 추가한다.
12. 유한한 definite 지식베이스의 전방 추론은 최소 고정점이자 최소 모델에 도달한다.
13. 제약의 몸체가 모두 도출되면 $\bot$에 도달하여 UNSAT이다.
14. 전방 추론은 Horn 절에 대한 단위 전파와 같은 핵심 연산이다.
15. 유한한 명제 Horn-SAT는 입력 크기에 선형인 시간으로 풀 수 있다.
16. 함수 기호가 있는 FOL Horn 규칙은 무한 grounding 또는 비종료를 일으킬 수 있다.
17. 도출되지 않음은 고전논리적 부정의 증명과 다르다.

---

## 46. 다음 장 예고

다음 장에서는 Horn 규칙을 실제 프로그램처럼 사용하는 **논리 프로그래밍**을 다룬다.

핵심 질문은 다음과 같다.

- 선언적 프로그래밍은 명령형 프로그래밍과 무엇이 다른가?
- 사실과 규칙으로 프로그램을 어떻게 구성하는가?
- Prolog식 표기에서 질의와 목표는 어떻게 작성하는가?
- 변수 치환과 패턴 매칭은 규칙 적용을 어떻게 가능하게 하는가?
- 전방 추론과 후방 추론은 언제 어떻게 다른가?
- 규칙의 적용 과정을 어떻게 추적할 수 있는가?

$
\text{Horn clause}
\rightarrow
\text{사실과 규칙}
\rightarrow
\text{질의와 추론}
\rightarrow
\text{논리 프로그램}
$
