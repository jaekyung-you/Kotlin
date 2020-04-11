# Infix Funtion
* 두 개의 변수 가운데 오는 함수
* ex. Pair를 만드는 'to'

```kotlin
val map1 = mapOf(Pair("Key1", "Value1"), Pair("Key2", "Value2"))
val map2 = mapOf("Key1" to "Value1", "Key2" to "Value2")
```

#### Infix 함수 직접 정의 
* infix fun dispatcher.receiver(함수 이름): 리턴 타입{ 구현부 }
* dispatcher: infix 함수 전에 오는 객체
* receiver : Infix 함수 뒤에 오는 객체 

```kotlin
infix fun String.add(*other* :String): String{
  return this + *other* //this: dispatcher
}

val string = "Hello" add "World"  
println(string) // "HelloWorld"
```
dispatcher(Hello)와 receiver(World)가 합쳐져서 출력

* 클래스 내에서 infix 함수 정의
```kotlin
class MyString {
  var string = ""
  infix fun add(other : String) {
    this.string = this.string + other
  }
}

fun main(args:Array<String>){
  val myString = MyString()
  myString add "Hello"
  myString add "World"
  myString add "Kotlin"
  println(myString.string) // HelloWorldKotlin
}
```
