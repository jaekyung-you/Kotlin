# Destructuring Declaration
어떤 객체의 데이터를 변수들에 대입하는 기술

```kotlin
val persons = arrayListOf<Person>()
persons.add(Person("Jaekyung", 10))
persons.add(Persons("JK", 25))

for((name, age) in persons){
  println("name : $name, age: $age")
}

persons.map{
  println("name: ${it.name}, age: ${it.age}")
}

persons.map{ (name, age) ->
  println("name : $name, age : $age")
}
```

만약 변수를 사용하지 않는다면, _ 로 생략

```kotlin
val (name, _) = person
println("name : $name")

for((_, age) in persons){
  println("age : $age")
}
```

일반클래스에 Destructuring Declaration 사용
* componentN()를 **operator 메소드**로 구현

```kotlin
class Person(val name: String, val age: Int){
  operator fun component1() : String = name
  operaotr fun component2() : Int = age
}

val person = Person("Jaekyung", 25)
val (name, age) = person
```
