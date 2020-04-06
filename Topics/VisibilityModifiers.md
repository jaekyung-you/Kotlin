# Visibility Modifiers
* 접근(가시성)제한자는 객체가 공개되어야할 범위(ex.public, private..)를 지정
* 따로 표기하지 않으면 public이 기본으로 설정됨 = 누구나, 어디에서나 접근가능
* private : 파일 안에서만 접근 가능
* internal : 프로젝트 모듈 안에서 누구나 접근 가능
* protected : 클래스 내부와 상속받는 객체에서 접근 가능. 최상위(top-level)에 정의되는 클래스나 인터페이스 등에서는 사용할 수 없음

```kotlin
open class Outer{
  private val a = 1
  protected open val b = 2
  internal val c = 3
  val d = 4
  
  protected class Nested{
    public val e: Int = 5
  }
}

//Outer() class 를 상속받는 Subclass
class Subclass: Outer(){
  // a 접근X
  // b, c, d, Nested, e 접근O
  
  overrid val b = 5
}

class Unrelated(o: Outer){
  // o.a , o.b 접근X
  // o.c , o.d 접근O
  // Outer.Nested , Nested::e 접근X
}
```

* 생성자에도 접근제한자 붙일 수 있음
```kotlin
class Code private constructor(a:Int){
}
```
