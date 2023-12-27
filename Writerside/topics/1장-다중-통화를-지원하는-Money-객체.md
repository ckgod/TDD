# 1장. 다중 통화를 지원하는 Money 객체

다중 통화를 지원하는 Money 객체의 예로 다음과 같은 보고서가 있다고 하자.

<table>
<tr><td>종목</td><td>주</td><td>가격</td><td>합계</td></tr>
<tr><td>IBM</td><td>1000</td><td>25</td><td>25000</td></tr>
<tr><td>GE</td><td>400</td><td>100</td><td>40000</td></tr>
<tr><td> </td><td> </td><td>합계</td><td>65000</td></tr>
</table>

위 보고서에서 다중 통화를 지원하는 보고서를 만들기 위해서는 통화 단위를 추가해야 한다.

<table>
<tr><td>종목</td><td>주</td><td>가격</td><td>합계</td></tr>
<tr><td>IBM</td><td>1000</td><td>25USD</td><td>25000USD</td></tr>
<tr><td>Novartis</td><td>400</td><td>150CHF</td><td>60000CHF</td></tr>
<tr><td> </td><td> </td><td>합계</td><td>65000USD</td></tr>
</table>

| 기준  | 변환  | 환율  |
|-----|-----|-----|
| CHF | USD | 1.5 |

위와 같은 새로운 보고서를 생성하려면 어떤 테스트를 해야하고, 코드가 완성됐다는 걸 확신할 수 있을까?

```text
- 통화가 다른 두 금액을 더해서 주어진 환율에 맞게 변한 금액을 결과로 얻을 수 있어야 한다.
- 어떤 금액(주가)을 어떤 수(주식의 수)에 곱한 금액을 결과로 얻을 수 있어야 한다.
```

이제 테스트를 작성하기 위해 할일 목록을 적어보자.

할일 목록
: - $5 + 10CHF = $10 (환율이 2:1로 가정)
: - **$5 x 2 = $10**

위 할일 목록을 보며 테스트를 먼저 만들어보자. 어떤 테스트가 필요할까?

첫 번째 항목은 복잡해 보이니 다음 항목인 곱하기 먼저 테스트를 작성해보자.
테스트를 작성할 때에는 작은 것부터 시작하든지, 아니면 아예 손을 대지 않는게 좋다.

다음은 간단한 곱셈의 예이다.

```Kotlin
fun testMultiplication() {
    val five = Dollar(5)
    five.times(2)
    assertEquals(10, five.amount)
}
```

물론 위 코드가 공용 필드에다가, 부작용이 있을 수도 있고, 금액 계산에 정수형을 사용한다. 하지막 작은 단계에서 시작하는 것 뿐이다.
실패하는 테스트가 주어진 상태고 최대한 빨리 초록 막대를 보고 싶을 뿐이다.

할일 목록
: - $5 + 10CHF = $10 (환율이 2:1로 가정)
: - **$5 x 2 = $10**
: - amount를 private으로 만들기
: - Dollar side effect?
: - Money 반올림?

방금 작성한 코드는 컴파일조차 되지 않는다. 일단 컴파일조차 안되는 것부터 고쳐보자. 현재 4개의 컴파일 에러가 있다.
- Dollar 클래스가 없음
- 생성자가 없음
- times(int) 메서드가 없음
- amount 필드가 없음

위 컴파일 에러들을 제거해보자.
```Kotlin
class Dollar(amount: Int) {
    val amount: Int = 0

    fun times(multiplier: Int) {
        
    }
}
```
이와 같이 Dollar 클래스 추가로 컴파일 에러는 제거된다. 하지만 테스트를 실행하면 테스트가 실패하는 모습을 볼 수 있다.

![test1_fail.png](test1_fail.png)

테스트 리포트를 보면 10을 기대했지만 0이 나왔다는 결과를 알려준다.
이제 제시할 해법이 마음에 안 들지도 모르지만 당장은 완벽한 해법이 아니라 눈앞에 놓인 테스트를 통과하는 것일 뿐이다.

```Kotlin
val amount: Int = 10
```

![test2_success.png](test2_success.png)

이쯤와서 TDD의 주기를 다시 생각해보자.

<procedure title="TDD의 리듬">
    <step>
        <p>재빨리 테스트를 하나 추가한다.</p>
    </step>
    <step>
        <p>모든 테스트를 실행하고 새로 추가한 것이 실패하는지 확인한다.</p>
    </step>
    <step>
        <p>코드를 조금 바꾼다.</p>
    </step>
    <step>
        <p>모든 테스트를 실행하고 전부 성공하는지 확인한다.</p>
    </step>
    <step>
        <p>리팩토링을 통해 중복을 제거한다.</p>
    </step>
</procedure>

지금까지 위 주기의 1번부터 4번까지 수행하였다. 이제 중복을 제거해보자.
중복을 찾기 위해 코드를 비교할 수도 있지만 이번 경우는 테스트에 있는 데이터와 코드에 있는 데이터 사이에 존재한다.

```Kotlin
val amount = 5 * 2
```

무자비하게 중복을 제거해보자.

<procedure title="중복 제거하기">
    <step>
        <p>amount 초기화 코드 times() 메서드 안으로 옮기기
            <code-block lang="kotlin">
                class Dollar(amount: Int) {
                    val amount = 0
                    fun times(multiplier: Int) {
                        amount = 5 * 2
                    }
                }
            </code-block>
        </p>
    </step>
    <step>
        <p>5는 생성자에서 넘어오는 값이니 amount 값에 넣기
            <code-block lang="kotlin">
                class Dollar(var amount: Int) {
                    fun times(multiplier: Int) {
                        amount *= 2
                    }
                }
            </code-block>
        </p>
    </step>
    <step>
        <p>2는 인자 multiplier의 값이므로 대체
            <code-block lang="kotlin">
                class Dollar(var amount: Int) {
                    fun times(multiplier: Int) {
                        amount *= multiplier
                    }
                }
            </code-block>
        </p>
    </step>
</procedure>

이제 첫 번째 테스트에 완료 표시를 할 수 있게 됐다.

할일 목록
: - $5 + 10CHF = $10 (환율이 2:1로 가정)
: - ~~$5 x 2 = $10~~
: - amount를 private으로 만들기
: - Dollar side effect?
: - Money 반올림?

지금까지 한 작업들을 검토해보자.
다음과 같은 작업들을 해냈다.

- 우리가 알고 있는 작업해야 할 테스트 목록을 만들었다.
- 오퍼레이션이 외부에서 어떻게 보이길 원하는지 말해주는 이야기를 코드로 표현했다.
- JUnit에 대한 상세한 사항들은 잠시 무시하기로 했다.
- 스텁 구현을 통해 테스트를 컴파일했다.
- 끔찍한 죄악을 범하여 테스트를 통과시켰다.
- 돌아가는 코드에서 상수를 변수로 변경하여 점진적으로 일반화했다.
- 새로운 할일들을 한 번에 처리하는 대신 할일 목록에 추가하고 넘어갔다.

> **스텁 구현이란?**
> 
> 메서드의 서명부와 (반환값이 있을 경우) 반환 명령만 적는 식으로 이 메서드가 호출하는 코드가 컴파일 될 수 있도록 껍데기만 만들어두는 것을 뜻한다.
{ style="note" }
