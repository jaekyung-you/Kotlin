# Scope Functions

```kotlin
Person("Alice", 20, "NewYork").let{
  println(it)
  it.moveTo("London")
  it.incrementAge()
  println(it)
}
```
Person 객체를 **리시버** , let 다음의 {...}을 **람다함수**

![ScopeFunctions](https://user-images.githubusercontent.com/59492694/78527500-fe0bf980-7817-11ea-96f6-e84e8c4860ec.png)
