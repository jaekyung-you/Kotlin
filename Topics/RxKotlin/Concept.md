# Concept
* 옵저버 패턴을 이용하여 구독자에게 변경사항을 알려줌
* ~~데이터를 요청하여 가공하는 것이 아니라~~ 데이터 변경시 요청 받겠다는 **구독**을 한 다음,
 **변경사항(event)** 이 발생하면 이를 전달받음
* 하나의 값보다는 data stream을 반환

# Observable
* Consumer가 소비하는 **값을 생성**
* Consumer들은 Observable에 **Subscribe(구독)** 을 신청 -> Observable은 값을 생성하여 Consumer에게 **push 방식으로 값 전달**

### Observable의 주요 event
* **onNext(item T)** : 값을 전달할 때 호출하여 값을 넘김
* **onError(e: Throwable)** : 에러가 발생하면 호출
* **onSubscribed(d: Disposable)** : 구독을 신청하면 호출
  * 이 때 Disposable 객체는 Observer가 구독을 해제할 때 사용
* **onComplete()** : 가지고 있는 값을 모두 전달하면 호출

```kotlin
val observer: Observer<String> = object: Observer<String>{
  override fun onComplete(){
    println("onComplete()")
  }
  
  override fun onNext(item: String){
    println("onNext() - $item")
  }
  
  override fun onError(e: Throwable){
    println("onError() - ${e.message}")
  }
  
  override fun onSubscribe(d: Disposable){
    println("onSubscribe() - $d")
  }
}
```

# Observable의 생성

### 1. create

```kotlin
fun main(args: Array<String>){
  val observable1 = Observable.create{
    it.onNext(1)
    it.onNext(2)
    it.onComplete()
  }
  
  val observer : Observer = object: Observer{
    override fun onComplete() = println("onComplete()")
    override fun onNext(item: Int) = println(onNext() - $item)
    override fun onError(e: Throwable) = println("onError() - ${e.message}")
    override fun onSubscribed(d: Disposable) = println("onSubscribe() -$d")
  }
  
  observable1.subscribe(observer)
}
```

### 2. from XXX

```kotlin
fun main(args: Array<String>){
  val list = listOf(1,2,3)
  val listOb = Observable.fromIterable(list)
  
  val call = Callable<Int>{ 4 }
  val callOb = Observable.fromCallable(call)
  
  val future = object: Future<Int>{
    override fun get() = 5
    
    override fun get(timeout: Long, unit: TimeUnit) = 6
    
    override fun isDone() = true
    
    override fun cancel(mayInterruptIfRunning : Boolean) = false
    
    override fun isCancelled() = false
  }
  
  val futureOb = Observable.fromFuture(future)
  
  val observer: Observer<Int> = object : Observer<Int> {
    override fun onComplete() = println("onComplete()")
    override fun onError(e: Throwable) = println("onError - ${e.message}")
    override fun onNext(item : Int) = println("onNext - $item")
    override fun onSubscrbie(d: Disposable) = println("onSubscribe() - $d")
  }
  
  listOb.subscribe(observer)
  callOb.subscribe(observer)
  futureOb.subscribe(observer)

}


결과
onSubscribe() - io.reactivex.internal.operators.observable.ObservableFromIterable$FromIterableDisposable@6bf2d08e 
onNext() - 1
onNext() - 2
onNext() - 3
onComplete()
onSubscribe() - 0 
onNext() - 4
onComplete()
onSubscribe() - 0 
onNext() - 5
onComplete()

```
* **fromIterable()** : list 같이 iterable을 지원하는 instance를 Observable 형태로 변경
  * 각 개별 아이템이 하나씩 전달 listOf(1,2,3) -> 1 한 번, 2 한 번, 3한 번
  * val listOb = listOf(1,2,3).toObservable()
* **fromCallable()** : Callable 객체를 Observable 형태로 변경
  * call() 함수의 return 값이 전달
* **fromFuture()** : Future 객체를 Observable 형태로 변경
  * get() 함수의 return 값이 전달
  
### 3. just, range, empty, interval, timer
**just** : 받은 인자를 그대로 전달(list든, map이든). 모든 item 전달이 완료되면 onComplete() 호출
**range** : 특정 범위만큼 수를 생성하여 
**emtpy** : 아무것도 전달하지 않고 onComplete()만 호출
**interval** : 특정 시간 간격으로 0부터 숫자를 증가시키면서 반환
**timer** : 주어진 시간에 한번만 값을 전달

```kotlin
fun main(args: Array<String>){
 val list = listOf(1,2,3)
 val num = 3
 val str = "wow!"
 val map = mapOf(1 to "one", 2 to "two")
 
 val justOb = Observable.just(list, num, str, map)
 
 val observer : Observer<Any> = object : Observer<Any>{
  override fun onComplete() = println("onComplete()")
  override fun onError(e: Throwable) = println("onError - ${e.message}")
  override fun onNext(item : Int) = println("onNext - $item")
  override fun onSubscrbie(d: Disposable) = println("onSubscribe() - $d")
 
 }
 
 justOb.subscribe(observer)
 
 Observable.range(1,3).subscribe(observer)
 
 Observable.empty().subscribe(observer)
 
 Thread() {Observable.interval(100, TimeUnit.MILLISECONDS).subscribe(observer)}.start()
 
 // 0.3초간 main thread를 대기시킴
 val th1 = Thread() {Thread.sleep(300)} // main 함수이기 때문에 sleep하지 않으면 process가 죽음. observer의 수행은 background에서 하고 main thread는 0.3초 block 시킴
 th1.start()
 th1.join()
 
 val th2 = Thread() {Thread.sleep(300)}
 th2.start()
 th2.join()
}

결과
onSubscribe() - io.reactivex.internal.operators.observable.ObservableFromArray$FromArrayDisposable@15615099 
onNext() - [1, 2, 3]
onNext() - 3
onNext() - wow!
onNext() - {1=one, 2=two}
onComplete()

onSubscribe() - 0 
onNext() - 1
onNext() - 2
onNext() - 3
onComplete()

onSubscribe() - INSTANCE 
onComplete()

onSubscribe() - null 
onNext() - 0
onNext() - 1

onSubscribe() - null 
onNext() - 0
onComplete()

```
  
  
  
  
  
  
  
  
  
  
  
  
