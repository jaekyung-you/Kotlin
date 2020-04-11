# Generics
* 클래스나 함수를 정의할 때 타입을 확실하게 정하지 않는 것
* < T >

### Generic 함수 정의

```kotlin
fun <T> addNumbers(num1 : T, num2: T) : T{
  return num1.toDouble() + num2.toDouble()) as T 
}

fun main(args:Array<String>){
  println(addNumbers(10,20)) // 30
  println(addNumbers(10.1, 20.1)) // 30.200000000000003
}
```

### Generic 클래스 정의
```kotlin
class Rectangle<T>(val width: T, val height: T){
}

class Rectangle<T, K>(val width: T, val height: T, val name: K){
}

fun main(args:Array<String>){
  val rec1 = Rectangle<Double>(10,20)
  val rec2 = Rectangle<String>("aa", "bb")
  val rec3 = Rectangle(10,20)
  val rec4 = Rectangle<Int, Int, String>(10, 20, "rec4")
}
```

### Constraints(제한, 제약)
* Rectangle("aa", "bb")와 같이 숫자가 아닌 인자도 허용
* 변수의 자료형에 제한을 걸고 싶다면

```kotlin
class Rectangle<T: Number>(val width: T, val height: T){
  fun getArea(): T {
    return (width.toDouble() * height.toDouble()) as T
  }
}
```

* 2개 이상의 제약을 걸고 싶다면 **where** 사용

```kotlin
class Rectangle<T>(val width: T, val height: T)
  where T: Number, T: Comparable<T>{
  fun getArea(): T {
    return (width.toDouble() * height.toDouble()) as T
  }
}
```

### Invariance(불변성)

```kotlin
class Double: Number, Comparable< Double >
```
Double은 Number를 상속하고, Double의 Super Class는 Number.(Double은 Number의 한 종류)
하지만, Rectangle< Double >의 Super Class는 Rectangle< Number >이 아님(Rectangle< Double >은 Rectangle< Number > 종류가 아님)
즉, 두 개의 타입이 서로 상속 관계이지만 Generic class의 상속 관계는 아님 = **Inheritance**

코틀린에서 Generic의 모든 type은 Invariance하다.
Invariance ↔ Covariance (In/Out 키워드로 변경)

### Covariance(공변성, 함께 변한다)
Number가 Double의 super class일 때, Rectangle < Number > 가 Rectangle< Double > 의 super class 이면,
**Covariance**라고 한다.

#### 1. out
out이 타입의 상속구조와 Generic의 상속구조가 같다는 것을 정의하여
Rectangle< Double >이 Rectangle< Number >의 하위 클래스라고 인식

```kotlin
class Rectangle<out T: Number>(val width: T, val height: T){
}

fun main(args:Array<String>){
  val derivedClass = Rectangle<Double>(10.5, 20.5)
  val baseCalss: Rectangle<Number> = derivedClass
}
```

#### 2. in
in은 타이브이 상위/하위 클래스 구조가 Generic에서는 반대 방향의 상위/하위 클래스 구조를 갖는 것
이 때, 두 타입의 관계가 **Contravariance**(반공변성)이다.
Rectangle< Double >이 Rectangle< Number >의 상위 클래스로 변환

```kotlin
class Rectangle< in T: Number >(val width : T, val height: T){
}

fun main(args:Array<String>){
  val baseClass = Rectangle<Number>(10.5, 20.5)
  val derivedCalss = Rectangle<Double> = baseClass
}

```

![generic](https://user-images.githubusercontent.com/59492694/79039471-6b56ca80-7c1c-11ea-927c-1984c768884e.png)

