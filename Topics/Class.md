# Class 및 생성자 정의
* Java 멤버변수(field) + getter/setter 메소드 = Kotlin에서 property
* Java는 생성자 정의 필수 <-> Kotlin에서 생성자 생략 가능
* Class정의 시 생성자도 함께 정의 가능 ex. class Person(firstName: String)

```kotlin
class Person(firstName: String){
  private var firstName = ""
  var lastName = "" // 변경가능하므로 get/set 메소드 모두 생성
  val old = 5 // 변경불가하므로 자바의 final과 동일 -> get 메소드만 생성
  
  // 초기화
  init{
    this.firstName = firstName
    this.lastName = "Code"
  }
}

fun main(args:Array<String>){
  val person = Person("Jaekyung")
  println(person.firstName)
  println(person.lastName)
  println(person.old)
  person.lastName = "You"
}
```

```kotlin
class Person{
  var firstName = ""
  var lastName = ""
  constructor(firstName : String) {
    this.firstName = firstName
  }
  constructor(firstName: String, lastName: String) {
    this.firstName = firstName
    this.lastName = lastName
  }
}

fun main(args:Array<String>){
  val person1 = Person("Jaekyung")
  val person2 = Person("Jaekyung", "You")
}
```

* getter/setter 생성되면 변수에 값을 변경 or 리턴만 가능 
* 다른 계산하거나 로그를 출력하려면 프로퍼티에 get(), set()함수를 정의
```kotlin
class Rectangle {
  var width = 10
    set(value){
      field = value / 2
    }
  var height = 10
    set(value){
      field = value / 2
    }
  var area : Int = 0
    get() = width * height
}

fun main(args:Array<String>){
  val rect = Rectangle()
  println("width : ${rect.width} , height: ${rect.heigth}, area: ${rect.area}")
  
  rect.width = 40
  rect.height = 40
  println("width : ${rect.width} , height: ${rect.heigth}, area: ${rect.area}")
}

실행결과
width : 10, height : 10, area: 100 
width : 20, height : 20, area: 400 
```



