Observable : 생산자 역할 / Observer(=Consumer): 소비자 역할  
생산은 대량으로, 소비는 천천히 일어날 수 있음  
따라서, 소비자의 상황에 따라 생산속도를 조절하기 위해 사용하는 채널 **BackPressure** 라고 하며
**Flowable** 은 backpressure를 지원함  
memory 에러 방지할 수 있지만 속도 느려짐 -> 생산의 갯수가 수 천, 수 만 개 일때 사용하고 그 외에는 observable 사용

공통 format : runBlocking{}
```kotlin
fun main(args:Array<String>) = runBlocking<Unit>{
  ...
  runBlocking{ delay(xxx)}
  ...
}
```
runBlocking{..}은 내부 코드가 전부 종료될 때까지 기다림  
delay()는 suspend함수로 해당 시간만큼 해당 line에서 머무름. 단, thread 자체가 block되지는 않음  

# Flowable
* Observable의 생성과 동일한 API 사용 ex.range(), interval(), just(), generate()
* Observable에 toFlowable()를 이용하여 BackPressure 지원

```kotlin
val observable = Observable.just(1,2,3,4,5)
val flowable = observable.toFlowable(BackpressureStrategy.BUFFER)
```

```kotlin
fun main(args: Array<String>){
  val observable = Observable.range(1,10)
  observable.map{
    val str = "Create item $it"
    runBlocking{ (delay(100) }
    println("$str - ${Thread.currentThread().name}")
    str
  }
  .observeOn(Schedulers.computation())
  .subscribe({
    println("Consume $it - ${Thread.currentThread().name}")
    runBlocking{ delay(200) }
  })
  delay(1000)
}
```
Observable에서 데이터를 100ms 단위로 생성하고, 소비에는 200ms의 delay가 걸림  
**observeOn** 을 이용하여 subscriber가 실행되는 thread를 **worker thread로 변경** 
-> 생산과 소비가 각각 다른 thread에서 수행(동시에 수행)  
생산하는 속도가 빨라 소비 작업이 뒤로 밀리고 나중에 몰려 OutOfMemory 발생 가능

```
결과
Create item 1 - main
Consume item 1 - RxComputationThreadPool-1
Create item 2 - main
Consume item 2 - RxComputationThreadPool-1
Create item 3 - main
Create item 4 - main
Create item 5 - main
Consume item 3 - RxComputationThreadPool-1
Create item 6 - main
Consume item 4 - RxComputationThreadPool-1
Create item 7 - main
Create item 8 - main
Consume item 5 - RxComputationThreadPool-1
Create item 9 - main
Create item 10 - main
Consume item 6 - RxComputationThreadPool-1
Consume item 7 - RxComputationThreadPool-1
Consume item 8 - RxComputationThreadPool-1
Consume item 9 - RxComputationThreadPool-1
Consume item 10 - RxComputationThreadPool-1
```

### <<Flowable로 변경 후>>
* 기본 128개 저장할 수 있는 buffer 제공 -> 150개 숫자를 delay없이 생산하고, 처리 당 1ms 시간 소요 걸리도록
* 128개가 생산되면 일단 생산 중단 -> 96개가 소비되면 다시 생산 시작하여 속도 조절
(버퍼의 threadhold 값이 128-96=32이기 때문)

```kotlin
fun main(args: Array<String>) = runBlocking<Unit>{
  val flowable = Flowable.range(1,150)
  flowable.map{
    println("Mapping item $it")
    it
  }
  .observeOn(Schedulers.computation())
  .subscribe({
    println("Received item $it")
    runBlocking{ delay(10) }
  })
 
 delay(10000)
}

결과
Mapping item 1
...
Mapping item 5
Received item 1
Mapping item 6
Mapping item 7
...
Mapping item 128
Received item 2
...
Received item 96
Mapping item 129
...
Mapping item 150
Received item 97
...
Received item 150

```

* Flowable은 ~~observer~~ 대신, backpressure를 지원하는 Subscriber를 사용
* request() 함수를 이용해서 수신할 데이터의 개수를 요청

```kotlin
fun main(args: Array<String>){
  Flowable.range(1,150)
          .map{
            println("Mapping item $it - ${Thread.currentThread().name}")
            it
          }
          .observeOn(Schedulers.computation())
          .subscribe(MySubscriber())
   delay(5000)     
}

class MySubscriber: Subscriber<Int>{
  override fun onComplete() = println("onComplete()")
  override fun onNext(t: Int){
    runBlocking{delay(10)}
    println("onNext(): $t - ${Thread.currentThread().name}")
  }
  override fun onSubscribe(s: Subscription){
    s.request(Long.MAX_VALUE) // 생산만 되고 실제 소비X
  }
  override fun onError(e: Throwable) = e.printStackTrace()
}

결과
Mapping item 1 - main

Mapping item 2 - main

...

Mapping item 127 - main

Mapping item 128 - main

onNext(): 1 - RxComputationThreadPool-1

onNext(): 2 - RxComputationThreadPool-1

...

onNext(): 95 - RxComputationThreadPool-1

onNext(): 96 - RxComputationThreadPool-1

Mapping item 129 - RxComputationThreadPool-1

Mapping item 130 - RxComputationThreadPool-1

...

Mapping item 149 - RxComputationThreadPool-1

Mapping item 150 - RxComputationThreadPool-1

onNext(): 97 - RxComputationThreadPool-1

onNext(): 98 - RxComputationThreadPool-1

...

onNext(): 149 - RxComputationThreadPool-1

onNext(): 150 - RxComputationThreadPool-1

onComplete()
```
```kotlin
...
 override fun onSubscribe(s: Subscription){
    s.request(Long.MAX_VALUE) // range(1,150) 전체를 받지 않았기 때문에 onComplete() 호출X. 소비를 다 못 했기 때문에 추가적인 나머지 생산도 X
  }
  ...
  
결과
Mapping item 1 - main

Mapping item 2 - main

...

Mapping item 127 - main

Mapping item 128 - main

onNext(): 1 - RxComputationThreadPool-1

onNext(): 2 - RxComputationThreadPool-1

onNext(): 3 - RxComputationThreadPool-1

onNext(): 4 - RxComputationThreadPool-1

onNext(): 5 - RxComputationThreadPool-1

```
# BackpressureStrategy
* create를 통해 flowable를 생성했을 때, backpressure에 대한 처리 옵션을 설정 가능
* 옵션에는 Missing, Error, Buffer, Drop, Latest 가 있음

### BackressureStrategy.BUFFER
* 무제한 버퍼 제공
* 제한 없이 생산하여 버퍼에 계속 쌓임
* 중간중간 데이터를 소비하고 생산이 모두 끝나면 버퍼에 쌓인 모든 데이터를 일괄적으로 처리 

```kotlin
fun main(args: Array<String>) = runBlocking<Unit>{
  val flowable = Flowable.create({
  for(i in 1..200){
    println("send item $i")
    it.onNext(i)
    runBlocking{delay(10)} // 생산속도 10ms
  }
  it.onComplete()  
  }, BackPressureStrategy)
  
  val waitingJob = launch {
    delay(Long.MAX_VALUE)
  } // runBlocking 블럭을 유지하기 위해 observing 이 끝날 때까지 대기하기 위해 사용
  
  flowable.observeOn(Schedulers.computation())
          .subscribe(MySubscriber(waitingJob))
}

class MySubscriber(val job: Job) : Subscriber<Int>{
  override fun onSubscribe(s: Subscription){
    s.request(200)
  }
  override fun onComplete(){
    println("onComplete()")
    job.cancel()
  }
  override fun onError(e: Throwable){
    println("onError: $e")
    job.cancel()
  }
  override fun onNext(t: Int){
    runBlocking{delay(100)}
    println("onNext(): $t - ${Thread.currentThread().name}")
  }
}


결과
send item 1

send item 2

...

send item 190

onNext(): 21 - RxComputationThreadPool-1

send item 191

...

send item 199

onNext(): 22 - RxComputationThreadPool-1

send item 200

onNext(): 23 - RxComputationThreadPool-1

onNext(): 24 - RxComputationThreadPool-1

...

onNext(): 199 - RxComputationThreadPool-1

onNext(): 200 - RxComputationThreadPool-1

onComplete()
```

### BackpressureStrategy.ERROR
* 소비쪽에서 생산을 따라잡을 수 없는 경우 error를 발생싴킴
* 기본 버퍼인 128개 제공

```kotlin
fun main(args:Array<String>) = runBlocking<Unit>{
  val flowable : Flowable = Flowable.create({
  
  
  }, BackpressureStrategy.ERROR)
  
  flowable.observeOn(Schedulers.computation())
          .subscribe(MySubscriber())
          
  delay(1000)        
}

class MySubscriber: Subscriber<Int>{
  ...
  override fun onError(e: Throwable) = e.printStackTrace()   
}
```

### BackpressureStrategy.DROP
* 수신자가 처리 중에 생산자로부터 전달받으면 해당 데이터 버림
* 

### BackpressureStrategy.LATEST
