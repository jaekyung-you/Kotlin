# Inline function
고차함수(함수를 인자로 전달 or 함수를 리턴)를 사용하면 메모리 할당 및 함수 호출로 runtime overhead 발생
inline function은 내부적으로 **함수 내용을 호출되는 위치에 복사** -> Runtime overhead 줄임

* 일반 함수보다 성능 좋음 
* 내부적으로 코드를 복사하기 때문에, 인자로 전달받은 함수는 다른 함수로 전달되거나 참조될 수 없다.
* 코드 양이 많은 함수를 inline하면, bytecode 양이 많아질 수 있고 최적화되지 않을 수 있다.
* 1~3줄 정도의 길이의 함수에 사용하는 것이 효과적

```kotlin
inline fun someMethod(a: Int, func: () -> Unit): Int{
  func()
  return 2*a
}
```
# Noinline keyword
모든 인자를 inline으로 처리하고 싶지 않을 때

```kotlin
inline fun newMethod(a: Int, func: () -> Unit, noinline func2 :() -> Unit){
  func()
  someMethod(10, func2)
}

fun someMethod(a: Int, func: () -> Unit) : Int{
  func()
  return 2*a
}

```
