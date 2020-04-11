# Delegate Pattern
* 어떤 기능을 자신이 처리하지 않고 다른 객체에게 **위임**하여 그 객체가 일을 처리하도록

#### 상속(Inheritance, is-a)
* 클래스의 변수 & 메소드 모두 받아 ~~재구현 필요~~
* 객체의 유연성 떨어짐 -> Composition(Aggregation)으로 구현

#### 구성(Composition, Aggregation, has-a)
* 상속이 아닌, 클래스 안에 객체를 소유하고 있는 관계
* Delegate Pattern은 Composition에 해당
* by로 형식적인 코드를 줄일 수 있음

* 기본 Delegate Pattern
```kotlin
interface IWindow(){
  fun getWidth(): Int
  fun getHeight(): Int
}

open class TransparentWindow: IWindow{
  override fun getWidth(): Int{
    return 100
  }
  override fun getHeight(): Int{
    return 150
  }
}

class UI(window: IWindow): IWindow{
  val mWindow: IWindow = window
  
  override fun getWidth(): Int{
    return mWindow.getWidth()
  }
  override fun getHeight(): Int{
    return mWindow.getHeight()
  }
}

fun main(args:Array<String>){
  val window: IWindow = TransparentWindow() // TransparentWindow가 모든 일 위임
  val ui = UI(window) // TransparentWindow를 넘겨받음
  println("ui.getWidth()", "ui.getHeight()") // 100,150 
}
```
#### by를 사용한 Delegate Pattern 예제
* boilerplate code 생략 가능
* IWindow by window 를 사용하면 Delegate Pattern 코드 자동으로 작성

```kotlin
interface IWindow(){
  fun getWidth(): Int
  fun getHeight(): Int
}

open class TransparentWindow: IWindow{
  override fun getWidth(): Int{
    return 100
  }
  override fun getHeight(): Int{
    return 150
  }
}

class UI(window: IWindow) : IWindow by window{

}

// main함수는 동일하게 구성
fun main(args:Array<String>){
  val window: IWindow = TransparentWindow() // TransparentWindow가 모든 일 위임
  val ui = UI(window) // TransparentWindow를 넘겨받음
  println("ui.getWidth()", "ui.getHeight()") // 100,150 
}
```
