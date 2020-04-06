# First-Class Functions(일급함수)
* 함수가 객체로 취급될 수 있음
```kotlin
val hello : () -> String = {"hello world"}
```

* 함수 객체를 인자로 넘길 수 있어야 함
```kotlin
val hello : () -> String = {"hello world"}
fun printHello(func : () -> String){
  print("${func()}")
}

fun main(args:Array<String>){
  printHello(hello)
}

실행결과: hello world
```

* 함수 객체를 함수의 결과로 리턴할 수 있음
```kotlin
val hello : () -> String = {"hello world"}
fun returnHello() : () -> String{
  return hello
}

fun main(args: Array<String>){
  val returned:() -> String = returnParamFunc(hello)
  print("${returned()}")
}

실행결과: hello world
```
# High-Order Functions (고계,고차함수)
* 함수의 인자로 함수를 넘기거나 함수를 리턴

```kotlin
val hello : () -> String = {"hello world"}
fun returnHello() : () -> String{
  return hello
}

fun main(args: Array<String>){
  val returned:() -> String = returnParamFunc(hello)
  print("${returned()}")
}

실행결과: hello world
```

(Int, Int) -> String
() -> String   인자가 없으면 괄호 안 void


# Lambda expression
* lambda = 익명함수 = 이름이 없는 함수 = 1회용 함수
* 함수를 따로 생성하지 않고 중간에 lambda 만듦 -> 코드의 가독성 높아짐
* 함수형 프로그래밍에서 자주 사용

```kotlin
// 익명함수를 생성하여 greeting에 할당
val greeting : () -> Uniot = {println("Hello")}

// 익명함수 호출
greeting()

실행결과: Hello
```

```kotlin
fun main(args:Array<String>) {
  // 익명함수를 생성하여 greeting에 할당
  val greeting2 = {name: String, age: String -> "Hello. ${name}'s age : $age"}

  //익명함수 호출
  val result = greeting2("Jaekyung", "25")
  println(result)
}

실행결과: Hello. Jaekyung's age : 25
```
* 인자 타입 생략
```kotlin
val greeting2: (String, String) -> String = {name, age -> "Hello. ${name}'s age : $age"}
val result = greeting2("Jaekyung", "25")
```

* 인자 선언 생략
```kotlin
val greeting2: (String) -> String = {"My name is $it"}
val result = greeting2("Jaekyung")
```

```kotlin
val numbers = listOf<Int>(5, 1, 3, 2, 9, 6, 7, 8, 4)
println(numbers)

val sorted = numbers.sortedBy({it})
val biggerThan5 = sorted.filter({it > 5})
```







