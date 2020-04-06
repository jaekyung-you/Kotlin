# 1.Static Nested Class (정적 중첩 클래스)

* Outerclass의 지역변수 접근 불가
* 외부에서 OuterClass.NestedClass로 객체 생성 가능

```kotlin
class OuterClass{
  val outerValue = 10

  class StaticNestedClass{
    private val innerValue = 20
    fun printItems() {
      //OuterClass.outerValue 접근 불가
      println("value: $innerValue")
    }
  }
}

fun main(args:Array<String>){
  val nested = OuterClass.StaticNestedClass()
  nested.printItems() 
}
```

# 2. Non-Static Nested Class
* inner class
* OuterClass의 지역변수 접근 가능
* 외부에서 OuterClass.NestedClass로 객체 생성 불가

```kotlin
class OuterClass {
    val outerValue = 10
    inner class InnerClass {
        private val innerValue = 20
        fun printItems() {
            println("inner: $value or ${this.value} or ${this@InnerClass.value}")
            println("outer: ${this@OuterClass.value}")
        }
    }

    fun printItems() {
        val inner = InnerClass()
        inner.printItems()
    }
}

fun main(args: Array<String>) {
    val outer = OuterClass()
    outer.printItems()
}
```
결과 
inner : 20 = 20 = 20 
outer : 10

    
    
    
      
      
      
      
