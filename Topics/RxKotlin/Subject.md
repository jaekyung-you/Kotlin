### 방법2. subject
* subject의 Observer 역할 : **여러 Observable에 구독을 신청** 할 수 있음
* subject의 Observable의 역할 : **받은 item 재배출** or **새로운 값 배출**
  (Observable이 Observer를 구독하는 구조)

### Observer 역할
#### PublishSubject
* 등록 시점부터 데이터를 전달 받음
* 실제 Observable은 Cold Observable로 생성했으나, Subject를 구독함으로써 Hot Observable처럼 동작

```kotlin
fun main(args: Array<String>){
  val observable = Observable.interval(100, TimeUnit.MILLISECONDS)
  val subject = PublishSubject.create<Long>() 
  observable.subscribe(subject) // observable이 PublishSubject를 구독
  runBlocking{ delay(300) }
  subject.subscribe{ println("1st : $it") } // $it = Long
  runBlocking{ delay(300) } 
  subject.subscribe{ println("2nd : $it") }
  runBlocking{ delay(300) } 
}

결과
1st: 3
1st: 4
1st: 5
1st: 6
2nd: 6
1st: 7
2nd: 7
1st: 8
2nd: 8
```
<img width="500" alt="PublishSubject" src="https://user-images.githubusercontent.com/59492694/79300966-3354cd80-7f23-11ea-9e76-5fd6c1cbfdba.png">

#### BehaviorSubject
* 등록 시점 직전에 배출된 값(a) 하나를 전달받고 시작(a,b)
```kotlin
fun main(args: Array<String>){
  val observable = Observable.interval(100, TimeUnit.MILLISECONDS)
  val subject = BehaviorSubject.create<Long>() 
  observable.subscribe(subject) // observable이 PublishSubject를 구독
  runBlocking{ delay(300) }
  subject.subscribe{ println("1st : $it") } // $it = Long
  runBlocking{ delay(300) } 
  subject.subscribe{ println("2nd : $it") }
  runBlocking{ delay(300) } 
}

결과
1st: 2
1st: 3
1st: 4
1st: 5
1st: 6
2nd: 6
1st: 7
2nd: 7
1st: 8
```

<img width="500" alt="BehaviorSubject" src="https://user-images.githubusercontent.com/59492694/79301409-8418f600-7f24-11ea-9143-05adc601a7d8.png">

#### AsyncSubject
* Observable의 마지막 값 한 번만 배출
* interval이면 무한대로 생성되기 때문에, just를 통해 지정된 개수만 배출

```kotlin
fun main(args: Array<String>){
  val observable = Observable.just(1,2,3,4,5,6,7,8,9,10) //10개만 배출
  val subject = AsyncSubject.create<Int>()
  observable.subscribe(subject)
  subject.subscribe{ println("1st: $it") }
  subject.subscrbie{ println("2nd: $it") }
}

결과
1st: 10
2nd: 10
```

<img width="500" alt="AsyncSubject" src="https://user-images.githubusercontent.com/59492694/79301518-c93d2800-7f24-11ea-8641-0e021619758d.png">

#### ReplaySubject
* 등록 시점 이전값을 모두 전달받은 후 새로 배출되는 값 전달(=cold observable)

```kotlin
fun main(args: Array<String>){
  val observable = Observable.interval(100, TimeUnit.MILLISECONDS)
  val subject = ReplaySubject.create()
  observable.subscribe(subject)
  runBlocking{ delay(300) }
  subject.subscribe{ println("1st : $it") }
  runBlocking{ delay(300) }
  subject.subscribe{ println("2nd : $it") }
  runBlocking{ delay(300) }
}

결과
2nd: 0
2nd: 1
2nd: 2
2nd: 3
2nd: 4
2nd: 5
1st: 6
2nd: 6
1st: 7
2nd: 7
1st: 8
2nd: 8

```

<img width="500" alt="ReplaySubject" src="https://user-images.githubusercontent.com/59492694/79301756-52ecf580-7f25-11ea-9d2e-36530733dc80.png">

### Observable 역할
* Observable없이 단독으로 subject만으로 Observable역할 수행하도록

```kotlin
fun main(args: Array<String>){
  val observer = object : Observer<String>{
    override fun onComplete(){}
    override fun onNext(item: String) = println("onNext() - $item")
    override fun onError(e: Throwable){}
    override fun onSubscribe(d: Disposable){}
  }  
    // Observable대신 subject
    val publicSubject = PublishSubject.create()
    publicSubject.subscribe(observer)
    
    val asyncSubject = AsyncSubject.create()
    asyncSubject.subscribe(observer)
    
    val behaviorSubject = BehaviorSubject.create()
    behaviorSubject.subscribe(observer)
    
    val replaySubject = ReplaySubject.create()
    replaySubject.subscribe(observer)
    
    (1..3).forEach{
      publishSubject.onNext("public: $it") //등록시점부터 모두
      asyncSubject.onNext("async : $it") // 마지막값 하나만
      behaviorSubject.onNext("behavior: $it") // 등록시점 직전값 + 등록시점이후 값 모두
      replaySubject.onNext("replay: $it") // cold Observable처럼 
    }
    
  AsyncSubject.onComplete() // 마지막 값만 배출하므로 명시적으로 onComplete()를 호출해야 전달받을 수 있음

}

```
