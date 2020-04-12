# Annotation

### @JvmName
코틀린을 바이트코드로 변환할 때 JVM Signature를 변경할 때
즉, 자바에서 호출되는 코틀린 함수의 이름이 변경

```kotlin
fun foo(a: List<String>){
  println("foo(a: List<String>)")
}

fun foo(a: List<Int>){
  println("foo(a: List<Int>)")
}
```
두 개의 foo 함수는 바이트코드로 변경될 때 시그니쳐가 동일(인자가 List<>이기 때문)
따라서, 컴파일 에러 발생

코틀린에서 정의된 함수 이름을 사용 ↔ 자바는 변경된 이름으로 호출
```kotlin
@JvmName("fooListString")
fun foo(a: List<String>){
  println("foo(a: List<String>)")
}

@JvmName("fooListInt")
fun foo(a: List<Int>){
  println("foo(a: List<Int>)")
}

fun main(args: Array<String>){
  val listString = listOf("foo", "bar")
  foo(listString)
  
  val listInt = listOf(0,1,2)
  foo(listInt)
}
```

### @JvmStatic
static 변수의 get/set 함수를 자동으로 만들라

```kotlin
class Bar{
  companion object {
    @JvmStatic var barSize: Int = 0
  }
}
```

```Java
public final class Bar{
  private static int barSize;
  public static final int getBarSize(){
    return barSize;
  }
  
  public static final void setBarSize(int var0){
    barSize = var0;
  }
  
  public static final class Companion{
    public final int getBarSize(){
      return Bar.barSize;
    }
    public final void setBarSize(int var1){
      Bar.barSize = var1;
    }
  }
}

Bar.getBarSize();
Bar.setBarSize(10);
Bar.Companion.getBarSize();
Bar.Companion.setBarSize(10);
등으로 접근
```

### @JvmField
get/set을 생성하지 말라는 의미

```kotlin
class Bar{
  @JvmField
  var barSize = 0
}
```

```Java
public final class Bar{
  @JvmField
  public int barSize;
}
```

### @Throws
코틀린 함수가 예외를 던질 수 있는 것 표시

```Java
public static final void convertStringToInt(@NotNull String str) throws NumberFormatException{
  ...
}
```

```kotlin
@Throws(NumberFormatException::class)
fun convertStringToInt(str : String){
  ...
}
```

### @JvmOverloads
코틀린 함수의 오버로딩 메소드를 생성

```kotlin
class Bar{
  @JvmOverloads constructor(
  var name: String, var size: Int = 0, var max: Int = 0){
    init{
      println("Bar init: $name, $size, $max")
    }
  }
}

```



