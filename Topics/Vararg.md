# Varang 가변인자
* 인자의 개수를 유동적으로 받을 수 있음
* 코드를 변경하지 않아도 됨

```kotlin
fun printString(vararg strings: String): Unit{
  for(string in strings){
    println(string)
  }
}

fun main(){
  printString("vararg1")
  printString("vararg1","vararg2")
  printString("vararg1","vararg2","vararg3")
}
```

#### 일반인자 + 가변인자
```kotlin
fun printString(title: String, vararg strings: String): Unit{
  println(title)
  for(string in strings){
    printnl(string)
  }
}

fun main(){
  printString("Title","vararg1","vararg2","vararg3")
  // 혹은 printString("vararg1","vararg2","vararg3", title = "Title")  명시
}
```

#### 배열을 가변인자로 넘기기

```kotlin
fun printString(vararg strings: String): Unit{
  for(string in strings){
    println(string)
  }
}

fun main(){
  val strings = arrayOf("vararg1","vararg2","vararg3")
  printString(strings[0], strings[1], strings[2]) 
  printString(*strings) // 배열의 원소 개수가 많을 경우
}
```
