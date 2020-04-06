# Companion Object
* Java에서 static(정적) 변수 혹은 메소드 정의 = Kotlin에서 Companion Object

```kotlin
class MyClass{
  companion object{
    val TAG = "MyClass"
    fun createFiles(){
    ...
    }
  }
}  

// 외부에서 접근
MyClass.TAG = MyClass.Companion.TAG
MyClass.createFiles() = MyClass.Companion.createFiles()

```
