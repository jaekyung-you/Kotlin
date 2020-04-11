# Pair

* 기본

```kotlin
val pair1 = Pair("Hello", "World")
val pair2 = Pair("Hello", 1234) // 자료형 달라도 ok
println(pair2.first) //"Hello"
println(pair2.second) //1234
println(pair1.component1()) //"Hello"
println(pair1.component2()) //"World"

val list = pair1.toList()
println(list[0]) // "Hello"
println(list[1]) // "World"
```

* 타입 명시
```kotlin
val pair1 = Pair<String, String>("Hello", "World")
val pair2 = Pair<String, Int>("Hello", 1234)

fun makePair(first: String, second: String): Pair<String, String>{
  return Pair(first, second)
}
val (hello, world) = makePair("Hello", "World")
```

* infix 함수
```kotlin
val pair: Pair<String, String> = "Hello" to "World"
val (hello, world) = "Hello" to "World"
```

# Triple
* Pair의 특성과 동일. 3번째 객체 리턴하는 **third** 와 **component3()** 추가
```kotlin
val triple = Triple<String, String, Int>("Hello", "World", 1234)
println(triple.third)
println(triple.component3())
```
