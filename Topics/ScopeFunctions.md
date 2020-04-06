# Scope Functions

**객체를 접근하는 방법(this, it)** 과 **return 값**으로 결정

```kotlin
Person("Alice", 20, "NewYork").let{
  println(it)
  it.moveTo("London")
  it.incrementAge()
  println(it)
}
```

Person 객체를 **리시버** , let 다음의 {...}을 **람다함수** 라고 한다


![ScopeFunctions](https://user-images.githubusercontent.com/59492694/78527500-fe0bf980-7817-11ea-96f6-e84e8c4860ec.png)

# let
* it으로 리시버에 접근, 람다함수의 마지막 결과를 return 
* 리시버의 여러 함수들을 호출할 때 사용
* 리시버가 null이 아닐 때만 동작할 수 있도록 설정 가능(numbers?.~)

```kotlin
val numbers = mutableListOf("one", "two", "three", "four", "five")
numbers?.map{it.length}
        .filter{it > 3}
        .let{println(it)}
```

# with
* this로 리시버에 접근, 람다함수의 마지막 결과를 return
* with(리시버 객체){람다함수} 
* 리턴값을 사용하지 않는 경우에 권장

```kotlin
val numbers = mutableListOf("one", "two", "three")
val firstAndLast = with(numbers){
  "The first element : ${first()}," +
  "The last element : ${last()}"
}

println(firstAndLast)
```

# run
* this로 리시버에 접근, 람다함수의 마지막 결과를 return
* 리시버.run{람다함수}

```kotlin
val service = MultiportService("https://example.kotlinlang.org", 80)
val result = service.run{
  port = 8080
  query(prepareRequest() + " to port $port")
}

// let으로 표현 시
val letResult = service.let{
  it.port = 8080
  it.query(it.prepareRequest() + " to port {$it.port})
}

val hexNumberRegex = run{
  val digits = "0-9"
  val hexDigits = "A-Fa-f"
  val sign = "+-"
  
  Regex("[$sign]?[$digits$hexDigits]+")
}
```
# apply
* this로 리시버에 접근, 리시버 객체를 return 

```kotlin
val adam = Person("Adam").apply{
  age = 32
  city = "London"
}
```

# also
* it으로 리시버에 접근, 리시버 객체를 return
* 리시버의 함수를 연속적으로 호출할 수 있음 ex. also 다음 add

```kotlin
val numbers = mutableListOf("one", "two", "three")
numbers
    .also { println("The list elements before adding new one: $it") }
    .add("four")
```




