# Cold Observable
* subscribe한 시점부터 데이터를 **처음부터** 순서대로 전부 내보냄
* ex. youtube, 뉴스 동영상

# Hot Observable
* 배출시점 != 구독시점
* 구독 신청 이후의 데이터부터 받게 됨 ~~처음부터~~ 구독 신청 전의 데이터는 받을 수 없음
* Event를 전달받는 형태로 사용
* ex. 실시간 스트리밍

### 방법1. publish()
* Hot Observable 구현 방법 중 하나로, connect를 호출하면 배출 시작

```kotlin
fun main(args: Array<String>){
  val connectableObservable = (1..10).toObservable().publish()
  
  // 1번 구독자 등록
  connectableObservable.subscribe{ println("first subscriber: $it")}
  println("Add first subscriber.")
  
  // 2번 구독자 등록
  connectableObservable.map{ "second subscriber : $it"}
    .subscribe{ println(it)}
  println("Add second subscriber.")
  
  // observable connect(). 이 때서야 데이터가 배출
  connectableObservable.connect()
  
  // 3번 구독자 등록, connect() 이후는 배출이 완료됐으므로 출력X
  connectableObservable.subscribe{ println("Subscription 3 : $it")}

}

결과
Add first subscriber.
Add second subscriber.
first subscriber: 1
second subscriber: 1
first subscriber: 2
second subscriber: 2
first subscriber: 3
second subscriber: 3
first subscriber: 4
second subscriber: 4
first subscriber: 5
second subscriber: 5
first subscriber: 6
second subscriber: 6
first subscriber: 7
second subscriber: 7
first subscriber: 8
second subscriber: 8
first subscriber: 9
second subscriber: 9
first subscriber: 10
second subscriber: 10

```

```kotlin
fun main(args: Array<String>){
  val connectableObservable = Observable.interval(100, TimeUnit.MILLISECONDS).publish()
  
  // 1번 구독자 등록
  connectableObservable.subscribe{ println("1st subscriber : $it")}
  
  // 2번 구독자 등록
  connectableObservable.map{ "2nd subscriber : $it"}
                        .subscribe{ println(it)}
   
   // connect(). 1번과 2번 구독자 배출 시작
   connectObservable.connect()
   
   runBlocking{
    delay(300)
   }
   
   // 3번 구독자 등록
   connectableObservable.map{ "3rd subscriber: $it"}
                        .subscribe{ println(it)}
                        
   runBlocking{
    dealy(300)
   }
}

결과
1st subscriber: 0
2nd subscriber: 0
1st subscriber: 1
2nd subscriber: 1
1st subscriber: 2
2nd subscriber: 2
1st subscriber: 3
2nd subscriber: 3
3rd subscriber: 3
1st subscriber: 4
2nd subscriber: 4
3rd subscriber: 4
1st subscriber: 5
2nd subscriber: 5
3rd subscriber: 5

```
### 방법2. subject
* subject의 Observer 역할 : **여러 Observable에 구독을 신청** 할 수 있음
* subject의 Observable의 역할 : **받은 item 재배출** or **새로운 값 배출**

#### PublishSubject
* 등록 시점부터 데이터를 전달 받음





