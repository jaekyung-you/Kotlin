# Singleton
#### 개념
  - 어플리케이션이 시작될 때 어떤 클래스가 **최초 한번** 메모리 할당를 할당(static)하고 그 메모리에 인스턴스를 만들어 사용하는 디자인패턴
  - 생성자가 여러 번 호출되더라도 실제로 생성되는 객체는 하나고 이후 호출된 생성자는 최초 생성된 객체를 반환한다
  - 단 하나의 인스턴스(객체)를 생성해 사용하는 디자인 패턴

#### 사용 이유
  - 고정된 메모리 영역을 얻으면서 한번의 new로 인스턴스를 사용 -> 메모리 낭비 방지
  - 싱글톤으로 만들어진 클래스의 인스턴스는 전역 인스턴스 -> 다른 클래스의 인스턴트들이 사용 가능
  - 공통된 객체를 여러개 생성해서 사용하는 경우에 사용
  
#### 문제점
   - 싱글톤 인스턴스가 너무 많이 사용될 경우 다른 인스턴스 간 결합도가 높아져 객체 지향 설계와 모순됨
   - 수정과 테스트 어려움
   - 멀티스레드 환경에서 동기화처리 안하면 인스턴스가 두 개 생성될 수도
   
   
# Object
* class와 달리, singleton을 이용하기 위해 object를 사용
* 익명객체를 생성할 때 사용

##### 1. 싱글턴 클래스 정의를 위한 object

```kotlin
// object를 사용해서 class가 singleton으로 동작
object CarFactory{
  val cars = mutableListOf<Car>()
  
  fun makeCar(horsepowers: Int): Car{
    val car = Car(horsepowers)
    cars.add(car)
    return car
  }
} // CarFactory 객체는 한번만 생성

fun main(args:Array<String>){
  val car = CarFactory.makeCar(150)
  println(CarFactory.cars.size) // 총 car의 개수 반환
}
```

##### 2. 싱글턴 클래스 정의를 위한 companion object

```kotlin
// 좀 더 직관적인 표현
class Car(val horsepower:Int){
  companion object Factory {
    val cars = mutableListOf<Car>()
    
    fun makeCar(horsepowers : Int): Car{
      val car = Car(horsepowers)
      cars.add(car)
      return car
    }
  }
}

fun main(args:Array<String>){
  val car1 = CarFactory.makeCar(150)
  val car2 = Car.Factory.makeCar(150)
  println(CarFactory.cars.size) // 총 car의 개수 반환
}
```
##### 3. object를 익명객체로 사용
* 1회용으로 사용 -> 이름 필요없음

```kotlin
interface Vehicle{
  fun drive(): String
}

fun start(vehicle: Vehicle) = println(vehicle.drive())

fun main(args:Array<String>){
  start(object : Vehicle{
    override fun drive() = "Driving really fast"
  })
}
```

 
 
 
 
