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

## Flowable
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

<<Flowable로 변경 후>>
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




