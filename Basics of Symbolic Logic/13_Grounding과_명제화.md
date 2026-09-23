# 13장. Grounding과 명제화

## 1. 이 장의 목표

지금까지 FOL 문장을 만들고, 구조에서 해석하고, 자연연역 규칙으로 증명하는 방법을 배웠다. 이제 변수가 있는 FOL 표현을 계산 가능한 명제논리 문제로 바꾸는 연결 고리를 살펴본다.

이 장을 마치면 다음을 할 수 있다.

1. grounding과 명제화의 목적을 설명할 수 있다.
2. ground term, ground atom, ground formula를 구분할 수 있다.
3. 치환을 이용해 FOL 식의 ground instance를 만들 수 있다.
4. 고정된 유한 영역에서 전칭문과 존재문을 전개할 수 있다.
5. 양화사 전개가 정확하려면 어떤 영역 가정이 필요한지 설명할 수 있다.
6. ground atom을 명제변수로 대응시킬 수 있다.
7. grounding된 지식과 질의를 SAT 문제로 바꿀 수 있다.
8. 함수 기호가 무한히 많은 ground term을 만들 수 있음을 설명할 수 있다.
9. 변수 수와 ground term 수에 따른 grounding 크기를 계산할 수 있다.
10. Herbrand 우주와 Herbrand 기반의 기본 직관을 설명할 수 있다.
11. 모든 FOL 문제를 유한하게 명제화할 수 없는 이유를 설명할 수 있다.

### 핵심 질문

- 변수에 구체적인 항을 넣는 것만으로 어떻게 명제논리 문제가 되는가?
- $\forall xP(x)$를 언제 $P(a)\land P(b)$처럼 전개할 수 있는가?
- ground atom을 명제변수로 바꿔도 무엇이 보존되는가?
- 함수 기호는 왜 grounding을 끝나지 않게 만들 수 있는가?
- Herbrand 관점은 영역을 어떻게 항의 세계로 바꾸는가?

---

## 2. 증명에서 계산으로

12장에서는 다음과 같은 형식적 증명을 구성했다.

$
\Gamma\vdash\varphi
$

컴퓨터가 논리적 결론을 찾는 또 다른 방법은 추론 문제를 이미 잘 다룰 수 있는 계산 문제로 변환하는 것이다.

이 강의의 연결 흐름은 다음과 같다.

$
\text{FOL 표현}
\rightarrow
\text{Grounding}
\rightarrow
\text{명제화}
\rightarrow
\text{SAT}
$

grounding은 변수와 양화사를 구체적인 ground term의 조합으로 펼친다. 명제화는 각 ground atom을 하나의 명제변수처럼 다룬다. 그 결과 제한된 FOL 문제를 명제논리의 만족 가능성 문제로 바꿀 수 있다.

### 중요한 한계

이 변환은 항상 유한하게 끝나는 만능 절차가 아니다. 영역, 이름, 함수 기호에 대한 조건에 따라 grounding 결과가 유한할 수도 있고 무한할 수도 있다.

---

## 3. Grounding이란 무엇인가

**Grounding**은 변수가 포함된 식에 변수 없는 항을 대입하여 변수 없는 사례를 만드는 과정이다.

다음 식을 보자.

$
Human(x)\rightarrow Mortal(x)
$

$x$에 `socrates`를 대입하면

$
Human(socrates)\rightarrow Mortal(socrates)
$

를 얻는다.

이 식은 더 이상 변수를 포함하지 않는다. 원래 식의 **ground instance**다.

### Grounding의 목적

- 변수가 있는 일반 규칙을 구체적인 사례들로 만든다.
- 양화문을 유한한 논리곱 또는 논리합으로 펼친다.
- ground atom을 불리언 변수로 대응시킨다.
- SAT solver나 규칙 엔진이 처리할 수 있는 입력을 만든다.

---

## 4. Ground라는 말의 뜻

논리에서 `ground`는 보통 **변수가 없다**는 뜻이다.

| 표현 | 변수 포함 여부 | ground 여부 |
|---|---:|---:|
| $x$ | 있음 | 아니오 |
| $f(x)$ | 있음 | 아니오 |
| $alice$ | 없음 | 예 |
| $f(alice)$ | 없음 | 예 |
| $P(x)$ | 있음 | 아니오 |
| $P(alice)$ | 없음 | 예 |
| $R(f(alice),bob)$ | 없음 | 예 |

`ground`와 `참`은 다른 개념이다.

> ground 식은 변수가 없는 식이지, 자동으로 참인 식이 아니다.

$P(alice)$는 ground atom이지만 구조에 따라 참일 수도 있고 거짓일 수도 있다.

---

## 5. Ground term

**Ground term**은 변수를 포함하지 않는 항이다.

### 5.1 상항

개체 상항은 ground term이다.

$
alice,\quad bob,\quad book1
$

### 5.2 함수항

함수 기호의 모든 인자가 ground term이면 결과도 ground term이다.

$
parent(alice)
$

$
favoriteBook(bob)
$

$
parent(parent(alice))
$

### 5.3 재귀적 정의

1. 모든 개체 상항은 ground term이다.
2. $t_1,\ldots,t_n$이 ground term이고 $f$가 $n$항 함수 기호라면 $f(t_1,\ldots,t_n)$도 ground term이다.
3. 위 규칙으로 만들어지지 않는 표현은 ground term이 아니다.

---

## 6. Ground atom과 ground formula

### 6.1 Ground atom

술어의 모든 인자가 ground term인 원자식을 **ground atom**이라고 한다.

$
Student(alice)
$

$
Reads(alice,book1)
$

$
Likes(parent(alice),favoriteBook(bob))
$

동일성 원자식도 양쪽 항이 ground이면 ground atom이다.

$
parent(alice)=bob
$

### 6.2 Ground formula

변수가 하나도 없는 식을 ground formula라고 한다.

$
Student(alice)\rightarrow Learns(alice)
$

$
Reads(alice,book1)\land\neg Lost(book1)
$

ground formula는 여러 ground atom과 연결사로 이루어질 수 있다.

---

## 7. 치환

치환은 변수에 항을 대응시키는 유한한 사상이다.

$
\theta=\{x/alice,\;y/book1\}
$

이는 $x$를 `alice`로, $y$를 `book1`로 바꾸라는 뜻이다.

식 $\varphi$에 치환 $\theta$를 적용한 결과를 다음처럼 쓴다.

$
\varphi\theta
$

### 예시

$
\varphi=Reads(x,y)\rightarrow Knows(x,y)
$

이면

$
\varphi\theta
=
Reads(alice,book1)\rightarrow Knows(alice,book1)
$

이다.

치환 결과에 변수가 없으면 $\theta$는 이 식에 대한 grounding 치환이다.

---

## 8. 한 변수의 grounding

다음 규칙을 보자.

$
Bird(x)\rightarrow Flies(x)
$

사용 가능한 ground term이 `tweety`, `opus` 두 개라면 다음 두 사례를 만들 수 있다.

$
Bird(tweety)\rightarrow Flies(tweety)
$

$
Bird(opus)\rightarrow Flies(opus)
$

각 사례는 원래 규칙의 변수 $x$에 서로 다른 ground term을 넣은 결과다.

### 질문

이 두 사례만 만들면 원래 전칭 규칙을 완전히 대신할 수 있는가?

그 답은 `tweety`와 `opus`가 양화 영역의 모든 개체를 실제로 빠짐없이 나타내는지에 달려 있다.

---

## 9. 여러 변수의 grounding

다음 이항 관계 규칙을 보자.

$
Parent(x,y)\rightarrow Older(x,y)
$

ground term 집합이

$
T=\{alice,bob\}
$

라면 가능한 치환은 네 개다.

$
\{x/alice,y/alice\}
$

$
\{x/alice,y/bob\}
$

$
\{x/bob,y/alice\}
$

$
\{x/bob,y/bob\}
$

따라서 네 ground instance가 생긴다.

변수가 두 개라고 해서 서로 다른 항만 넣는 것은 아니다. $x$와 $y$가 같은 개체를 가리킬 수도 있으므로 모든 조합을 고려해야 한다.

---

## 10. Grounding 집합

식 또는 규칙 집합 $K$와 ground term 집합 $T$가 주어졌다고 하자.

$K$의 모든 변수에 $T$의 ground term을 넣어 얻는 모든 ground instance의 집합을 다음처럼 생각할 수 있다.

$
Ground(K,T)
$

예를 들어

$
K=\{P(x)\rightarrow Q(x)\}
$

$
T=\{a,b\}
$

이면

$
Ground(K,T)=
\{P(a)\rightarrow Q(a),\;P(b)\rightarrow Q(b)\}
$

이다.

실제 시스템은 모든 조합을 무조건 만들지 않고 필요한 사례만 생성할 수 있다. 그러나 개념적으로는 가능한 치환들의 공간이 grounding의 출발점이다.

---

## 11. 유한 영역 전개의 정확한 조건

다음 전개는 자주 사용된다.

$
\forall xP(x)
\quad\Longleftrightarrow\quad
P(a)\land P(b)
$

그러나 표준 FOL에서 언제나 성립하는 논리적 동치는 아니다.

정확한 전개를 위해서는 다음 조건이 필요하다.

1. 평가할 영역이 고정된 유한 집합이다.
2. 사용할 ground term들이 영역의 모든 개체를 빠짐없이 지시한다.
3. 양화사가 바로 그 고정된 영역을 돈다는 것을 알고 있다.

예를 들어

$
D=\{d_1,d_2\}
$

이고 $a$가 $d_1$, $b$가 $d_2$를 지시한다고 주어지면 전개가 정확하다.

### 11장과의 연결

표준 FOL에서는 이름 없는 개체가 있을 수 있다. 따라서 단지 언어에 `a`, `b`가 있다는 사실만으로 영역이 두 개체뿐이라고 가정해서는 안 된다.

---

## 12. 전칭 양화사의 유한 전개

영역의 모든 원소를 나타내는 ground term이

$
T=\{t_1,t_2,\ldots,t_n\}
$

이라면

$
\forall x\,\varphi(x)
$

는 고정된 이 영역에서 다음 논리곱으로 전개된다.

$
\varphi(t_1)\land\varphi(t_2)\land\cdots\land\varphi(t_n)
$

### 예시

영역이 `alice`, `bob`, `charlie` 세 사람으로 정확히 구성되었다면

$
\forall x\,Registered(x)
$

는

$
Registered(alice)
\land Registered(bob)
\land Registered(charlie)
$

로 평가할 수 있다.

전칭문은 모든 사례가 참이어야 하므로 논리곱이 된다.

---

## 13. 존재 양화사의 유한 전개

같은 조건에서

$
\exists x\,\varphi(x)
$

는 다음 논리합으로 전개된다.

$
\varphi(t_1)\lor\varphi(t_2)\lor\cdots\lor\varphi(t_n)
$

### 예시

영역이 `alice`, `bob`, `charlie` 세 사람이라면

$
\exists x\,Winner(x)
$

는

$
Winner(alice)
\lor Winner(bob)
\lor Winner(charlie)
$

가 된다.

존재문은 적어도 한 사례가 참이면 되므로 논리합이 된다.

---

## 14. 양화사 전개와 중복 이름

서로 다른 상항이 같은 개체를 가리킬 수 있다.

$
a^{\mathcal M}=b^{\mathcal M}
$

이때 $P(a)\land P(b)$에는 의미상 같은 개체의 사례가 중복될 수 있다. 중복은 전칭 전개의 진릿값을 바꾸지 않는다.

마찬가지로 $P(a)\lor P(b)$에서도 같은 사례가 중복되어도 진릿값은 바뀌지 않는다.

그러나 ground atom을 서로 독립적인 명제변수로 단순 취급할 때는 문제가 생길 수 있다. $a=b$인 구조라면 $P(a)$와 $P(b)$의 진릿값은 같아야 하기 때문이다.

따라서 명제화 방법은 다음 중 하나를 명확히 해야 한다.

- 이름이 서로 다른 개체를 가리킨다는 고유 이름 가정
- 실제 영역 원소를 직접 나타내는 표준 이름 사용
- 동일성 및 합동성 제약의 추가

---

## 15. 다중 전칭문의 전개

고정된 영역이

$
T=\{a,b\}
$

일 때

$
\forall x\forall yR(x,y)
$

는 모든 순서쌍을 검사한다.

$
R(a,a)
\land R(a,b)
\land R(b,a)
\land R(b,b)
$

변수가 두 개이고 ground term이 두 개이므로 $2^2=4$개의 사례가 생긴다.

### 일반화

서로 독립적으로 대입되는 변수가 $k$개이고 ground term이 $n$개라면 가능한 치환 수는 최대

$
n^k
$

개다.

---

## 16. 전칭과 존재가 섞인 전개

고정된 영역 $T=\{a,b\}$에서

$
\forall x\exists yR(x,y)
$

를 전개하면

$
(R(a,a)\lor R(a,b))
\land
(R(b,a)\lor R(b,b))
$

가 된다.

바깥 전칭은 각 $x$ 사례를 논리곱으로 묶고, 안쪽 존재는 각 $x$에 가능한 $y$ 사례를 논리합으로 묶는다.

반대로

$
\exists y\forall xR(x,y)
$

는

$
(R(a,a)\land R(b,a))
\lor
(R(a,b)\land R(b,b))
$

가 된다.

두 결과는 일반적으로 동치가 아니다. grounding에서도 양화사 순서를 보존해야 한다.

---

## 17. Ground atom을 명제변수로 보기

ground atom은 내부에 변수가 없으므로 하나의 참·거짓 단위로 취급할 수 있다.

예를 들어 다음 대응을 정한다.

$
p_{ab}\;\widehat{=}\;Parent(alice,bob)
$

$
q_b\;\widehat{=}\;Mortal(bob)
$

그러면

$
Parent(alice,bob)\rightarrow Mortal(bob)
$

는

$
p_{ab}\rightarrow q_b
$

라는 명제논리식으로 읽을 수 있다.

### 핵심

명제화는 ground atom의 내부 구조를 잠시 감추고 각 ground atom 전체를 하나의 불리언 변수로 취급한다.

---

## 18. 명제화 대응표

다음 ground 지식 기반을 보자.

$
Human(socrates)
$

$
Human(socrates)\rightarrow Mortal(socrates)
$

대응표를 만든다.

| Ground atom | 명제변수 |
|---|---|
| $Human(socrates)$ | $H_s$ |
| $Mortal(socrates)$ | $M_s$ |

그러면 지식 기반은

$
H_s
$

$
H_s\rightarrow M_s
$

가 된다.

이제 modus ponens, 진리표, CNF, SAT 등 명제논리 도구를 사용할 수 있다.

---

## 19. 규칙 하나의 완전한 변환

다음 전칭 규칙과 사실을 보자.

$
\forall x(Human(x)\rightarrow Mortal(x))
$

$
Human(alice)
$

$
Human(bob)
$

영역이 정확히 $\{alice,bob\}$라고 가정한다.

### 19.1 Grounding

$
(Human(alice)\rightarrow Mortal(alice))
\land
(Human(bob)\rightarrow Mortal(bob))
$

### 19.2 명제화

$
H_a\rightarrow M_a
$

$
H_b\rightarrow M_b
$

사실은 $H_a$, $H_b$다.

### 19.3 결론

명제논리 추론으로 $M_a$와 $M_b$를 얻는다.

---

## 20. Grounding된 식을 CNF로 바꾸기

SAT solver의 입력은 보통 CNF다.

ground 규칙

$
Human(alice)\rightarrow Mortal(alice)
$

를 명제화하면

$
H_a\rightarrow M_a
$

이고, 조건문을 제거하면

$
\neg H_a\lor M_a
$

라는 하나의 절이 된다.

여러 ground 규칙은 여러 절의 논리곱이 된다.

$
(\neg H_a\lor M_a)
\land
(\neg H_b\lor M_b)
\land
H_a
\land
H_b
$

이 식은 SAT solver가 직접 처리할 수 있는 명제논리 CNF다.

---

## 21. Grounding과 SAT 추론

유한하게 grounding된 지식 기반을 $G(K)$, 질의를 $G(Q)$라고 하자.

질의가 지식에서 따라오는지 확인하려면 반례를 찾는다.

$
G(K)\land\neg G(Q)
$

- SAT이면 전제는 참이면서 질의는 거짓인 명제 할당이 있다.
- UNSAT이면 그런 반례가 없으므로 grounding된 문제에서 질의가 따라온다.

### 예시

$
K=\{H_a,\;H_a\rightarrow M_a\}
$

질의가 $M_a$라면

$
H_a\land(\neg H_a\lor M_a)\land\neg M_a
$

는 UNSAT이다. 따라서 $M_a$가 따라온다.

---

## 22. 무엇이 보존되는가

올바른 조건에서 grounding과 명제화는 원래 문제의 만족 가능성 또는 함축 관계를 명제논리 문제에 반영한다.

그러나 `올바른 조건`을 생략해서는 안 된다.

### 필요한 확인

1. 고려해야 할 ground term 집합이 완전한가?
2. 양화 영역을 빠짐없이 열거했는가?
3. 동일성에 필요한 제약을 반영했는가?
4. 함수항 깊이를 임의로 잘랐다면 결과가 불완전하지 않은가?
5. 사용한 규칙의 변수 의미가 보존되었는가?

일부 사례만 grounding해 SAT 결과가 나왔다고 해서 원래 FOL 전체의 결론을 자동으로 보장하지 않는다.

---

## 23. 동일성과 명제화

동일성이 없는 ground atom들은 서로 다른 명제변수처럼 취급하기 쉽다.

동일성이 있으면 추가 주의가 필요하다.

$
a=b
$

$
P(a)
$

이면

$
P(b)
$

도 성립해야 한다.

단순히 $a=b$, $P(a)$, $P(b)$를 서로 독립적인 불리언 변수로 만들면 이 관계가 사라진다.

### 두 접근

- 고정된 실제 영역에서 동일성을 미리 평가한다.
- 동일성의 반사성, 대칭성, 추이성, 술어·함수의 합동성 제약을 명제식에 추가한다.

따라서 동일성이 있는 명제화는 원자식 대응표만 만드는 것보다 더 많은 제약을 필요로 할 수 있다.

---

## 24. 함수 기호가 없는 경우

언어에 유한한 상항만 있고 양의 항수를 가진 함수 기호가 없다면 ground term의 수는 유한하다.

예를 들어 상항이

$
a,b,c
$

뿐이라면 ground term 집합은

$
\{a,b,c\}
$

다.

변수 수가 유한한 각 규칙은 유한한 수의 ground instance만 만든다.

이 조건은 Datalog과 같은 제한된 논리 프로그래밍 언어가 유한한 grounding과 계산을 설계하기 쉬운 이유 중 하나다.

---

## 25. 함수 기호와 무한한 항

일항 함수 기호 $f$와 상항 $a$가 있다고 하자.

다음 항들을 계속 만들 수 있다.

$
a
$

$
f(a)
$

$
f(f(a))
$

$
f(f(f(a)))
$

$
\cdots
$

각 항은 유한한 길이를 가지지만, 가능한 항 전체의 집합은 무한하다.

따라서 변수를 모든 ground term으로 치환하려 하면 grounding이 끝나지 않을 수 있다.

---

## 26. 항 깊이

함수항의 중첩 정도를 **항 깊이**로 측정할 수 있다.

한 가지 정의는 다음과 같다.

$
depth(a)=0
$

$
depth(f(t))=1+depth(t)
$

따라서

| 항 | 깊이 |
|---|---:|
| $a$ | 0 |
| $f(a)$ | 1 |
| $f(f(a))$ | 2 |
| $g(a,f(a))$ | 2 |

실제 시스템은 최대 항 깊이를 제한해 유한한 grounding을 만들기도 한다.

그러나 깊이 제한은 일반적으로 근사다. 제한보다 깊은 항이 필요한 증명이나 반례를 놓칠 수 있다.

---

## 27. Grounding 폭발

ground term 집합이 유한해도 결과가 매우 커질 수 있다. 이를 **grounding 폭발**이라고 한다.

변수가 $k$개이고 사용 가능한 ground term이 $n$개라면 한 규칙의 가능한 치환 수는 최대

$
n^k
$

다.

### 예시

ground term이 100개이고 변수가 3개라면

$
100^3=1,000,000
$

개의 치환이 가능하다.

규칙이 여러 개이고 각 instance에 여러 atom이 포함되면 생성되는 ground atom과 절의 수는 더 커진다.

---

## 28. Grounding 크기를 예측하기

다음 규칙을 보자.

$
R(x,y)\land S(y,z)\rightarrow T(x,z)
$

서로 다른 변수가 $x,y,z$ 세 개다.

ground term 수가 $n$이면 최대 $n^3$개의 instance가 생긴다.

| $n$ | 최대 instance 수 $n^3$ |
|---:|---:|
| 10 | 1,000 |
| 100 | 1,000,000 |
| 1,000 | 1,000,000,000 |

작은 규칙 하나도 영역이 커지면 급격히 팽창한다.

### 실제 수는 줄어들 수 있다

타입 제약, 이미 알려진 사실, 연결 조건, 관련성 분석을 사용하면 불가능한 치환을 미리 제거할 수 있다.

---

## 29. 모든 조합을 만들지 않는 방법

실제 추론 시스템은 가능한 모든 ground instance를 먼저 만드는 방식만 사용하지 않는다.

대표적인 아이디어는 다음과 같다.

- 질의와 관련된 규칙만 선택한다.
- 이미 존재하는 사실과 결합 가능한 치환만 만든다.
- 타입 또는 모드 제약으로 변수의 후보를 줄인다.
- 필요한 순간에만 instance를 만드는 lazy grounding을 사용한다.
- 규칙의 연결 구조를 이용해 join 순서를 최적화한다.
- 대칭적인 사례를 하나로 줄인다.

이러한 방법은 결과의 논리적 의미를 보존하면서 불필요한 중간 표현을 줄이는 것을 목표로 한다.

---

## 30. 모든 FOL 문제를 유한하게 명제화할 수 없는 이유

일반 FOL을 항상 유한한 SAT 문제로 바꾸는 절차는 존재하지 않는다.

주요 이유는 다음과 같다.

1. 논의 영역이 무한할 수 있다.
2. 유한한 이름만으로 모든 영역 원소를 나타내지 못할 수 있다.
3. 함수 기호가 무한히 많은 ground term을 만들 수 있다.
4. 필요한 항 깊이에 미리 정한 유한한 상한이 없을 수 있다.
5. 일반적인 FOL의 만족 가능성 및 타당성 문제는 결정 불가능하다.

따라서 유한 grounding은 FOL 전체의 완전한 대체물이 아니라 특정한 조건과 제한 아래에서 강력한 계산 기법이다.

---

## 31. 영역 폐쇄 가정과 고유 이름 가정

grounding 기반 시스템에서는 표준 FOL보다 강한 가정을 두기도 한다.

### 31.1 영역 폐쇄 가정

고려하는 ground term이 영역의 모든 개체를 나타낸다고 가정한다.

$
D=\{a,b,c\}
$

처럼 이름 목록이 영역 전체를 닫는다.

### 31.2 고유 이름 가정

서로 다른 이름이 서로 다른 개체를 가리킨다고 가정한다.

$
a\neq b
$

표준 FOL은 이 두 가정을 자동으로 포함하지 않는다. 어떤 시스템이 이 가정을 사용하는지 명시해야 한다.

### 31.3 폐쇄세계 가정과는 다르다

영역 폐쇄는 `어떤 개체가 존재하는가`를 제한한다. 폐쇄세계 가정은 `증명되지 않은 사실을 거짓으로 볼 것인가`에 관한 별도의 가정이다.

---

## 32. Herbrand 우주

Herbrand 관점은 영역의 개체를 언어의 ground term으로 표현한다.

**Herbrand 우주**는 언어의 상항과 함수 기호로 만들 수 있는 모든 ground term의 집합이다.

### 예시 1: 함수 기호 없음

상항이 $a,b$뿐이면

$
HU=\{a,b\}
$

이다.

### 예시 2: 일항 함수 기호 $f$

상항 $a$와 함수 $f$가 있으면

$
HU=\{a,f(a),f(f(a)),\ldots\}
$

이다.

### 상항이 하나도 없다면

Herbrand 우주가 비지 않도록 새 상항 하나를 추가해 시작하는 관례를 사용한다.

---

## 33. Herbrand 기반

**Herbrand 기반**은 언어의 술어 기호에 Herbrand 우주의 ground term을 넣어 만들 수 있는 모든 ground atom의 집합이다.

예를 들어

$
HU=\{a,b\}
$

이고 단항 술어 $P$, 이항 술어 $R$가 있으면

$
HB=\{P(a),P(b),R(a,a),R(a,b),R(b,a),R(b,b)\}
$

이다.

Herbrand 기반의 각 ground atom에 참·거짓을 지정하면 하나의 Herbrand 해석을 생각할 수 있다.

이 점이 ground atom을 명제변수처럼 보는 직관과 연결된다.

---

## 34. Herbrand 해석의 직관

Herbrand 해석에서는 ground term이 자기 자신을 나타내는 표준적인 항의 세계를 사용한다.

그 결과 다음이 단순해진다.

- 영역 원소를 별도의 외부 객체로 찾지 않는다.
- ground atom의 참·거짓 집합으로 해석을 표현한다.
- 절과 논리 프로그램의 만족 가능성을 ground instance로 분석한다.

### 주의

Herbrand 해석만 본다고 해서 일반 FOL 의미론이 사라지는 것은 아니다. 특정한 정리와 절 형식에서는 Herbrand 모델을 이용해 일반 모델의 존재 문제를 ground 수준으로 연결할 수 있다.

이 장에서는 그 증명보다 다음 직관에 집중한다.

$
\text{항으로 만든 영역}
\rightarrow
\text{ground atom의 집합}
\rightarrow
\text{명제적 해석}
$

---

## 35. 유한 Herbrand 우주의 조건

Herbrand 우주가 유한해지는 대표적인 조건은 다음과 같다.

1. 상항의 수가 유한하다.
2. 양의 항수를 가진 함수 기호가 없다.

이때 Herbrand 기반도 술어 기호와 항수가 유한하면 유한하다.

반대로 상항 하나와 일항 함수 하나만 있어도

$
a,f(a),f(f(a)),\ldots
$

가 생겨 Herbrand 우주가 무한해진다.

### 실용적 의미

함수 기호를 제한하거나 제거하면 전체 ground 공간을 유한하게 유지하기 쉬워진다. Datalog이 일반적으로 함수 기호를 제한하는 이유와 연결된다.

---

## 36. Grounding, 명제화, CNF의 차이

세 변환은 서로 다른 일을 한다.

| 단계 | 입력 | 출력 | 핵심 변화 |
|---|---|---|---|
| Grounding | 변수가 있는 FOL 식 | ground formula들 | 변수를 ground term으로 대체 |
| 명제화 | ground formula | 명제논리식 | ground atom을 명제변수로 대응 |
| CNF 변환 | 명제논리식 | 절들의 논리곱 | 연결사 구조를 SAT 형식으로 변환 |

예를 들어

$
\forall x(P(x)\rightarrow Q(x))
$

는 고정된 $\{a,b\}$ 영역에서

$
(P(a)\rightarrow Q(a))\land(P(b)\rightarrow Q(b))
$

로 grounding되고, 명제화와 CNF 변환을 거쳐

$
(\neg p_a\lor q_a)\land(\neg p_b\lor q_b)
$

가 된다.

---

## 37. 자주 발생하는 오류

### 오류 1. ground를 참이라는 뜻으로 이해한다

ground는 변수 없음이라는 뜻이다.

### 오류 2. 언어에 있는 상항이 영역 전체라고 자동 가정한다

표준 FOL에는 이름 없는 개체가 있을 수 있다.

### 오류 3. 전칭을 일부 사례의 논리곱으로 바꾼다

모든 영역 원소가 열거되었는지 확인해야 한다.

### 오류 4. 존재 전개에서 논리곱을 사용한다

존재는 적어도 하나이므로 논리합이다.

### 오류 5. 다중 양화사의 순서를 바꾼다

$\forall x\exists y$와 $\exists y\forall x$의 전개 구조는 다르다.

### 오류 6. 서로 다른 ground atom의 의미적 제약을 무시한다

동일성이나 함수 합동성 때문에 atom들이 독립적이지 않을 수 있다.

### 오류 7. 함수항을 한 단계만 만들고 완전한 grounding이라고 한다

함수 기호가 있으면 더 깊은 항이 계속 생길 수 있다.

### 오류 8. grounding 크기를 ground term 수만으로 판단한다

변수 수에 따라 $n^k$로 증가한다.

### 오류 9. SAT 결과를 원래 FOL 전체의 결과로 바로 일반화한다

grounding의 완전성 조건을 확인해야 한다.

### 오류 10. 영역 폐쇄와 폐쇄세계를 혼동한다

하나는 개체 목록, 다른 하나는 알려지지 않은 사실의 진릿값에 관한 가정이다.

---

## 38. Grounding 절차

### 38.1 1단계: 변수와 양화사를 찾는다

각 규칙의 서로 다른 변수를 표시한다.

### 38.2 2단계: ground term 집합을 정한다

이 집합이 실제 영역 전체를 나타내는지, Herbrand 우주인지, 깊이 제한을 둔 근사인지 기록한다.

### 38.3 3단계: 치환을 생성한다

$k$개 변수에 $n$개 항을 대입하는 가능한 조합을 만든다.

### 38.4 4단계: ground instance를 만든다

같은 변수의 모든 출현에 같은 항을 넣는다.

### 38.5 5단계: 양화사 구조를 보존해 묶는다

전칭은 논리곱, 존재는 논리합이며 중첩 순서를 유지한다.

### 38.6 6단계: ground atom 대응표를 만든다

각 atom에 고유한 명제변수를 배정한다.

### 38.7 7단계: CNF와 SAT로 연결한다

조건문을 제거하고 필요하면 CNF로 변환한다.

### 38.8 8단계: 완전성과 크기를 점검한다

누락된 영역 원소, 함수항, 동일성 제약, grounding 폭발을 확인한다.

---

## 39. 수업 활동

### 활동 1. Ground 여부 분류

다음 표현을 ground term, ground atom, ground formula, non-ground로 분류한다.

1. $f(a)$
2. $P(f(a))$
3. $P(x)\rightarrow Q(a)$
4. $R(a,b)\land\neg S(b)$
5. $g(f(a),b)$

### 활동 2. 영역 가정 찾기

$\forall xP(x)$를 $P(a)\land P(b)$로 바꾼 풀이를 보고, 이 전개가 정확하기 위해 필요한 가정을 조별로 적는다.

### 활동 3. 양화사 전개

$T=\{a,b\}$에서 다음을 전개한다.

1. $\exists xP(x)$
2. $\forall x\forall yR(x,y)$
3. $\forall x\exists yR(x,y)$

### 활동 4. 명제화 대응표

ground formula에 나오는 atom을 찾아 명제변수를 배정하고 CNF로 바꾼다.

$
(P(a)\land R(a,b))\rightarrow Q(b)
$

### 활동 5. 폭발 계산

ground term 수와 변수 수가 주어졌을 때 가능한 치환 수를 계산한다.

- $n=5,k=2$
- $n=20,k=3$
- $n=100,k=4$

---

## 40. 연습문제

### 기본

1. ground term을 정의하라.
2. $f(a)$와 $f(x)$ 중 ground term을 고르라.
3. $R(a,f(b))$가 ground atom인지 판정하라.
4. $P(a)\rightarrow Q(b)$가 ground formula인지 판정하라.
5. $\theta=\{x/a,y/b\}$를 $R(x,y)\rightarrow S(y)$에 적용하라.

### 유한 영역 전개

영역의 모든 개체를 $a,b,c$가 나타낸다고 가정한다.

6. $\forall xP(x)$를 전개하라.
7. $\exists xQ(x)$를 전개하라.
8. $\forall x(P(x)\rightarrow Q(x))$를 전개하라.
9. $\forall x\exists yR(x,y)$를 전개하라.
10. $\exists y\forall xR(x,y)$를 전개하라.

### 명제화와 SAT

11. $Human(a)$와 $Mortal(a)$에 명제변수를 배정하고 $Human(a)\rightarrow Mortal(a)$를 명제화하라.
12. 문제 11의 식을 CNF 절로 바꾸라.
13. $H_a$와 $H_a\rightarrow M_a$에서 $M_a$가 따라오는지 SAT 반례 검사식으로 보이라.
14. $P(a)\lor P(b)$와 $\neg P(a)$가 있을 때 SAT 수준에서 무엇을 유도할 수 있는가?

### 크기와 한계

15. ground term이 10개이고 변수가 2개인 규칙의 최대 instance 수를 계산하라.
16. ground term이 50개이고 변수가 4개인 규칙의 최대 instance 수를 계산하라.
17. 상항 $a$와 일항 함수 $f$가 있을 때 처음 다섯 ground term을 쓰라.
18. 함수항 깊이를 2로 제한하면 어떤 완전성 문제가 생길 수 있는가?
19. 표준 FOL에서 상항 $a,b$만 보인다고 해서 영역이 $\{a,b\}$라고 할 수 없는 이유를 설명하라.
20. 영역 폐쇄 가정과 고유 이름 가정의 차이를 설명하라.

### 심화

21. $HU=\{a,b\}$이고 단항 술어 $P,Q$, 이항 술어 $R$가 있을 때 Herbrand 기반을 쓰라.
22. 함수 기호가 없는 유한 언어에서 Herbrand 우주가 유한한 이유를 설명하라.
23. 동일성이 포함된 명제화에서 추가 제약이 필요한 이유를 예로 설명하라.
24. grounding과 명제화와 CNF 변환을 서로 구분하라.

---

## 41. 연습문제 해설

### 문제 1

변수를 포함하지 않는 항이다.

### 문제 2

$f(a)$만 ground term이다. $f(x)$에는 변수 $x$가 있다.

### 문제 3

$a$와 $f(b)$가 모두 ground term이므로 ground atom이다.

### 문제 4

두 원자식에 변수가 없으므로 ground formula다.

### 문제 5

$
R(a,b)\rightarrow S(b)
$

### 문제 6

$
P(a)\land P(b)\land P(c)
$

### 문제 7

$
Q(a)\lor Q(b)\lor Q(c)
$

### 문제 8

세 조건문의 논리곱이다.

$
(P(a)\rightarrow Q(a))
\land(P(b)\rightarrow Q(b))
\land(P(c)\rightarrow Q(c))
$

### 문제 9

각 $x$ 사례마다 세 $y$ 사례의 논리합을 만들고, 세 묶음을 논리곱한다.

$
\bigvee_{y\in\{a,b,c\}}R(a,y)
\land
\bigvee_{y\in\{a,b,c\}}R(b,y)
\land
\bigvee_{y\in\{a,b,c\}}R(c,y)
$

### 문제 10

각 $y$ 후보에 대해 모든 $x$ 사례의 논리곱을 만들고, 세 묶음을 논리합한다.

$
\bigvee_{y\in\{a,b,c\}}
\bigwedge_{x\in\{a,b,c\}}R(x,y)
$

### 문제 11

$H_a\widehat{=}Human(a)$, $M_a\widehat{=}Mortal(a)$로 두면 $H_a\rightarrow M_a$다.

### 문제 12

$
\neg H_a\lor M_a
$

### 문제 13

$
H_a\land(\neg H_a\lor M_a)\land\neg M_a
$

는 UNSAT이므로 $M_a$가 따라온다.

### 문제 14

$P(a)$가 거짓이고 둘 중 하나는 참이어야 하므로 $P(b)$를 유도할 수 있다.

### 문제 15

$
10^2=100
$

### 문제 16

$
50^4=6,250,000
$

### 문제 17

$
a,f(a),f(f(a)),f(f(f(a))),f(f(f(f(a))))
$

### 문제 18

깊이 3 이상의 항이 필요한 모델, 반례, 증명을 놓칠 수 있으므로 일반적으로 불완전하다.

### 문제 19

표준 FOL은 이름 없는 개체를 허용하고, 서로 다른 상항이 같은 개체를 가리킬 수도 있다.

### 문제 20

영역 폐쇄는 이름 또는 ground term 목록이 모든 개체를 나타낸다는 가정이다. 고유 이름은 서로 다른 이름이 서로 다른 개체를 나타낸다는 가정이다.

### 문제 21

$
\{P(a),P(b),Q(a),Q(b),R(a,a),R(a,b),R(b,a),R(b,b)\}
$

### 문제 22

새 ground term을 만드는 양의 항수 함수가 없으므로 유한한 상항 목록에서 더 많은 항이 생성되지 않는다.

### 문제 23

$a=b$와 $P(a)$가 참이면 $P(b)$도 참이어야 한다. 원자들을 독립 불리언 변수로만 만들면 이 합동성 관계가 사라지므로 추가 제약이 필요하다.

### 문제 24

grounding은 변수를 ground term으로 대체한다. 명제화는 ground atom을 명제변수로 대응시킨다. CNF 변환은 명제논리식의 연결사 구조를 절들의 논리곱으로 바꾼다.

---

## 42. 60분 수업 운영안

### 0–7분: FOL에서 SAT로 가는 이유

- 증명과 변환 기반 계산을 비교한다.
- grounding과 명제화의 역할을 분리한다.

### 7–16분: Ground 표현

- ground term, ground atom, ground formula를 분류한다.
- 한 변수와 여러 변수의 치환을 연습한다.

### 16–28분: 유한 영역 양화사 전개

- 전칭은 논리곱, 존재는 논리합으로 전개한다.
- 영역 전체를 열거해야 한다는 조건을 강조한다.
- 다중 양화사 순서를 비교한다.

### 28–38분: 명제화와 SAT

- ground atom 대응표를 만든다.
- 조건문을 CNF 절로 바꾼다.
- $K\land\neg Q$의 UNSAT 검사를 수행한다.

### 38–48분: 함수와 폭발

- 함수항이 무한히 생성되는 과정을 본다.
- $n^k$로 instance 수를 계산한다.
- 모든 FOL의 유한 명제화가 불가능함을 설명한다.

### 48–56분: Herbrand 직관

- Herbrand 우주와 기반을 작은 언어에서 만든다.
- ground atom과 명제변수의 연결을 확인한다.

### 56–60분: 형성평가

- 전개 조건, 함수 기호의 영향, 세 변환의 차이를 확인한다.

### 수업에서 강조할 문장

> Grounding은 변수를 없애지만, 영역과 항의 세계가 유한하다는 사실까지 자동으로 보장하지는 않는다.

---

## 43. 형성평가

### 문항 1

$P(f(a))$는 왜 ground atom인가?

**정답:** $a$가 ground term이고 따라서 $f(a)$도 ground term이며 원자식에 변수가 없기 때문이다.

### 문항 2

$\forall xP(x)$를 $P(a)\land P(b)$로 정확히 전개하려면 무엇을 알아야 하는가?

**정답:** $a$와 $b$가 양화 영역의 모든 개체를 빠짐없이 나타낸다는 것을 알아야 한다.

### 문항 3

ground term이 $n$개이고 서로 다른 변수가 $k$개인 규칙의 가능한 치환 수는 최대 얼마인가?

**정답:** $n^k$개다.

### 문항 4

상항 $a$와 일항 함수 $f$가 있을 때 Herbrand 우주는 유한한가?

**정답:** 아니다. $a,f(a),f(f(a)),\ldots$를 무한히 만들 수 있다.

### 문항 5

grounding과 명제화의 차이는 무엇인가?

**정답:** grounding은 변수를 ground term으로 대체하고, 명제화는 결과의 ground atom을 명제변수로 대응시킨다.

---

## 44. 핵심 정리

1. grounding은 변수에 ground term을 대입해 ground instance를 만드는 과정이다.
2. ground term, ground atom, ground formula에는 변수가 없다.
3. 고정된 유한 영역의 모든 원소가 열거되면 전칭은 논리곱, 존재는 논리합으로 전개된다.
4. 표준 FOL에서는 언어의 상항이 영역 전체를 자동으로 열거하지 않는다.
5. 다중 양화문은 양화사 순서를 보존해 논리곱과 논리합을 중첩해야 한다.
6. ground atom 전체를 하나의 명제변수로 대응시키면 명제논리 도구를 사용할 수 있다.
7. grounding된 식을 CNF로 바꾸면 SAT solver로 만족 가능성과 반례를 검사할 수 있다.
8. 동일성이 있으면 atom 사이의 합동성 제약을 반영해야 한다.
9. 함수 기호가 없고 상항이 유한하면 ground term 집합을 유한하게 유지하기 쉽다.
10. 함수 기호는 $a,f(a),f(f(a)),\ldots$처럼 무한한 ground term을 만들 수 있다.
11. $n$개의 ground term과 $k$개의 변수는 최대 $n^k$개의 치환을 만든다.
12. Herbrand 우주는 모든 ground term, Herbrand 기반은 모든 ground atom의 집합이다.
13. 유한 grounding은 제한된 조건 아래에서 강력하지만 일반 FOL 전체의 유한한 대체물은 아니다.

---

## 45. 다음 장 예고

다음 장에서는 일반 FOL보다 제한적이지만 계산에 적합한 **Horn clause와 규칙 표현**을 다룬다.

핵심 질문은 다음과 같다.

- Horn clause는 어떤 절인가?
- definite clause는 왜 규칙처럼 읽을 수 있는가?
- 사실, 규칙, 목표, 제약은 어떻게 구분되는가?
- Horn 규칙의 몸체와 머리는 무엇인가?
- 전방 추론과 단위 전파는 어떻게 연결되는가?
- Horn-SAT는 왜 일반 SAT보다 다루기 쉬운가?

$
\text{FOL}
\supset
\text{Horn clause}
\supset
\text{definite clause}
\rightarrow
\text{논리 프로그램}
$
