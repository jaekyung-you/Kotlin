#### Observer Pattern
어떤 데이터의 값이 변경되었는지 알고 싶을때, 버튼이 눌려 어떤 옵션이 변경되었을 때 그 옵션에 맞게 다른 처리 -> Observer Pattern
코틀린의 Delegates는 프로퍼티를 Observable로 쉽게 만들어줌 
프로퍼티의 값이 변경될 때마다 Callback을 받아 다른 처리를 함

```kotlin
var observed = false
var max : Int by Deletages.observable(0) { property, oldValue, newValue ->
  println("Changing max to $newValue")
  observed = true
}

fun main(){
  println(max) // 0
  println("observed "${observed}") // false
  max = 10
  println(max) // 10
  println("observed "${observed}") //true
}
```

* 클래스 내에 사용하는 Delegates.Observable
```kotlin
class MyObservable{
  var value: Int by Delegates.observable(0){ property, oldVal, newVal ->
    onValueChanged()
  }
  private fun onValueChanged(){
    println("value has changed: $value")
  }
}

fun main(){
  val observable = MyObservable()
  obervable.value = 10
  obervable.value = -20
}
```
