# 18장. xUnit으로 가는 첫걸음

2부는 TDD를 통해 '실제' 소프트웨어를 만드는 예제이다.

우선 테스트 케이스를 만들고 테스트 메서드를 실행할 수 있어야 한다.

처음엔 테스트 케이스를 작성하기 위해 사용할 프레임워크를 테스트하기 위한 테스트 케이스를 작성해야 한다.
아직 프레임워크가 없기 때문에 첫 번째 단계는 수동으로 검증해야 한다.

일단 할일 목록을 적어보자.

할일 목록
: - **테스트 메서드 호출하기**
: - 먼저 setUp 호출하기
: - 나중에 tearDown 호출하기
: - 테스트 메서드가 실패하더라도 tearDown 호출하기
: - 여러 개의 테스트 실행하기
: - 수집된 결과를 출력하기

첫 번째 테스트로 메서드가 호출되면 true, 그렇지 않으면 false를 반환하는 프로그램을 짜보자.

메서드안에 플래그를 설정하는 테스트 케이스가 있다면, 
그 플래그를 출력해보고 수동으로 정상 동작하는지 한 번만 검증하면 이 과정을 자동화 할 수 있다.

```Kotlin
val test = WasRun("testMethod")
print(test.wasRun)
test.run()
print(test.wasRun)
```

먼저 false를 출력하고 testMethod가 실행된 후에는 true가 출력되어야 한다.
위 코드를 실행할 수 있게 WasRun클래스와 testMethod 함수를 작성해보자.

```Kotlin
class WasRun(val name: String) {
    var wasRun: Boolean = false
    
    fun testMethod() {
    }
}
```

이제 코드를 실행하면 false, false가 출력된다.
false, true가 출력되어야하니 testMethod안에서 플래그를 설정해준다.

```Kotlin
    fun testMethod() {
        wasRun = true
    }
```

![5902581j.png](5902581j.png)

원하는 결과를 얻었으니 리팩토링을 진행해야한다.
testMethod를 직접호출하는 대신 진짜 인터페이스인 run() 메서드를 사용하게 변경해보자.

테스트는 아래와 같이 변하게 된다.

```Kotlin
val test = WasRun("testMethod")
print(test.wasRun)
test.run()
print(test.wasRun)

class WasRun(val name: String) {
    var wasRun: Boolean = false

    fun testMethod() {
        wasRun = true
    }
    
    fun run() {
        testMethod()
    }
}
```

다음으로 testMethod를 동적으로 호출할 수 있게 해보자.
테스트 케이스의 이름과 같은 문자열을 갖는 필드가 주어지면, 해당 메서드를 실행할 수 있다.

```Kotlin
class WasRun(val name: String) {
    var wasRun: Boolean = false

    fun testMethod() {
        wasRun = true
    }

    fun run() {
        val method = this::class.members.find { it.name == name }
        method?.call(this)
    }
}
```

이제 WasRun 클래스는 메서드가 호출되었는지 그렇지 않은지 기억하는 일, 메서드를 동적으로 호출하는 일 두 가지의 일을 수행하게 되었다.
WasRun은 wasRun만 기억하는 일을 하게하도록 상위 클래스인 TestCase를 만들자.

```Kotlin
open class TestCase() {

}

class WasRun(val name: String) : TestCase() {
    ...
}
```

name 속성을 상위 클래스로 올리고, run 메서드는 상위 클래스에 있는 속성만 사용하므로 이것도 올리자.

```Kotlin
open class TestCase(val name: String) {
    fun run() {
        val method = this::class.members.find { it.name == name }
        method?.call(this)
    }
}

class WasRun(name: String) : TestCase(name) {
    var wasRun: Boolean = false

    fun testMethod() {
        wasRun = true
    }
}
```

지금까지 작성한 코드를 이용해서 다음처럼 바꿔 쓸 수 있다.

```Kotlin
class TestCaseTest(name: String) : TestCase(name) {
    fun testRunning() {
        val test = WasRun("testMethod")
        assert(!test.wasRun)
        test.run()
        assert(!test.wasRun)
    }
}

fun main(args:Array<String>) {
    TestCaseTest("testRunning").run()
}
```

할일 목록
: - ~~테스트 메서드 호출하기~~
: - 먼저 setUp 호출하기
: - 나중에 tearDown 호출하기
: - 테스트 메서드가 실패하더라도 tearDown 호출하기
: - 여러 개의 테스트 실행하기
: - 수집된 결과를 출력하기