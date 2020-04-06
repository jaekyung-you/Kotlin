변수 선언을 먼저하고 초기화를 뒤로 미룸 -> 성능 향상에 도움
ex. Rest API로 Github의 데이터를 가져올 때, 앱 시작할 때가 아닌 데이터를 화면에 보여줄 때 가져옴

# Late initialization
* lateinit 
* 필요할 때 초기화, 초기화하지 않고 쓰면 Exception 발생

```kotlin
class Rectangle {
  lateinit var area: Area
  fun initArea(param : Area): Unit{
    this.area = param
  }
}

class Area(val value: Int)

fun main(){
  val rect = Rectangle() // Area값이 뭔지도 모른 채 초기화
  rect.initArea(Area(10))
  println(rect.area.value)
}
```
#### 특징
* var만 사용 ~lateinit val area: Area~
* primitive type(Int, Boolean)은 사용할 수 없음 ~lateinit var width : Int~
* getter/setter 생성X ~lateinit var area: Area get(){ area; }~
* Nullable 불가 ~lateinit var area: Area?~


# Lazy initialization
* by lazy
* 변수 선언 시 초기화 코드도 정의, 변수가 사용될 때 변수 초기화

```kotlin
class Account(){
  val balance : Int by lazy{
    println("Setting balance!")
    100
  }
}

fun main(){
  val account = Account()
  println(account.balance)
  println(account.balance)
}


실행결과
Setting balance! // 한 번만 호출됨
100
100
```
#### 특징
* val만 사용
* primitive type 사용 가능
* Non-null, Nullabble 모두 사용 가능


