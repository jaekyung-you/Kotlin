# Subscribe

Observable에서 방출하는 값을 받기 위해 subscribe() 함수를 이용해 등록
param에 따라 다양한 정의
* subscribe() : Disposable
* subscribe(onNext: Consumer): Disposable
* subscribe(onNext: Consumer, onError: Consumer): Disposable
* subscribe(onNext: Consumer, onError: Consumer, onComplete: Action): Disposable
* subscribe(onNext: Consumer, onError: Consumer, onComplete: Action, onSubscribe: Consumer): Disposable

```kotlin
fun main(args: Array<String>){
  val observable = Observable.range(1,3)
  
  observable.subscribe(
    { item -> println("onNext - $item")},
    { err -> println("onError - $err")},
    { println("onComplete()")}
  )
}

결과
onNext - 1
onNext - 2
onNext - 3
onComplete()
```

# Disposable
Observer를 subscribe를 이용해 등록하면 onSubscribed(d: Disposable) = {...}으로 Disposable의 instance를 전달받음

Disposable의 interface
```kotlin
public interface Diposable {

  void dispose() // 구독 해지 
  
  boolean isDisposed() // retrun true if this resource has been disposed. 구독해지 여부
}
```
#### 방법1
```kotlin
fun main(args: Array<String>){
  val observable = Observable.interval(100, TimeUnit.MILLISECONDS)
  
  val observer = object : Observer<Long>{
    lateinit var disposable : Disposable
    
    override fun onComplete() = println("onComplete()")
    override fun onNext(item : Long){
      println("onNext- $item")
      if(item >= 5 && disposable.isDisposed == false){
        disposable.dispose() // item이 5이상에 dispose되지 않았으면 dispose
      }
    }
    override fun onError(e: Throwable) = println("onError() - ${e.message}")
    overrdie fun onSubscribe(d: Disposable) {
      println("onSubscribe() - $d")
      disposable = d
    }
  }
  
  observable.subscribe(observer)
  
  // 0.5초 대기 후 dispose() 호출 thread가 총 5개(0,1,2,3,4) 생성되므로
  Thread(){
    Thread.sleep(1000)
  }.apply{
    start()
    join()
  }

}

```
#### 방법2
```kotlin
fun main(args: Array<String>){
  val observable = Observable.interval(100, TimeUnit.MILLISECONDS)
  
  val disposable = observable.subscribe(
    { item -> println("onNext -> $item")},
    { err -> println("onError - $err")},
    { println("onComplete()")}
  )
  
  //0.5초 대기후 dispose() 호출
  Thread() {
    Thread.sleep(500)
    disposable.dispose()
    Thread.sleep(100)
  }.apply{
    start()
    join()
  }
}



```
