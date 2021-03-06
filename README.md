# RxSwift-Study

### ReactiveX
**: observabal streams으로 Async 프로그래밍을 하기 위한 API (즉, Swift로 Reactive 프로그래밍을 하기 위한 라이브러리)**

### Sync/Async

- Sync(동기) : 작업을 하면 다른 작업 못하게 막음
- Async(비동기) : 작업을 해도 다른 작업은 계속 실행됨

> **Sync를 Async로 하려면 DispatchQueue하면 됨**  
> - `DispatchQueue.global().async`은 concurrence(동시 발생)을 async방식으로 하는 것  
> - `DispatchQueue.main.async`은 main thread로 돌리는 것

### RxSwift

- Async 코드를 간결하게 쓰기위해 사용함
- RxSwift install하려면 pod file에 `pod 'RxSwift`만 명시해주면 됨
- PromiseKit과 비슷하지만, return 타입이 Observable타입
- return할 때, Observabal을 create하고 seal in에 넣어 `.onNext`로 보냄

### Observable

- Observer : Observable에 들어가는(?) 항목
- Observable : 하나 또는 연속된 항목(Observer)을 비동기적으로 배출하는 기능
- Observer는 Observable을 구독하고, Observable은 항목을 배출하거나 Observable의 메서드 호출을 통해 Observer에게 알람을 보냄

### Disposable

- Observable에서 `.subscribe`하면 return되는 타입
- Disposable을 통해 dispose하면 작업 취소됨
- error가 나거나 특정상황일 때 모든 작업을 취소하기 위해 사용
- RxSwift나 PromiseKit을 사용하지 않고 dipose하려면 OperationQueue와 귀찮은 작업들을 해야함

### DisposeBag : dispose를 담는 가방

- DisposeBag에 insert 되어있는 작업을 한번에 dispose해줌
- dipose한 DisposeBag 안의 Disposable들은 없어짐

### Operators (참고 : [RxMarble Diagram](https://rxmarbles.com))

1. just : creating operator

- 데이터가 모두 create되면 complete되는 Operator
- Observabal.just의 데이터가 `.subscribe`할 때 첫번째 인자로 그대로 내려옴(데이터 타입은 Array도 가능)
> **complete되면 DisposeBag에서 없어진다.(for 모든 Operator)**

2. From : creating operator

- Array에 있는 데이터 요소들을 하나씩 떼어서 내려줌(Array 요소 타입은 Any)
- Array에 있는 요소들을 모두 create되면 complete되는 Operator

3. Map : transforming operator

- `.just`를 거친 **데이터를 mapping하고 데이터로 반환**하는 Operator
- `.map`은 처음부터 연결되는 것이 아니라, **기존에 존재하는 stream에 연결하는 Operator**
> *예시1) Array인 경우, from을 거치고 map을 통해 `.count`로 mapping시켜 Int타입으로 바뀌고 값이 내려감*  
> *예시2) mapping을 연속해서 사용할 경우, 링크를 URL객체로 변환시키고 data를 가져와서 UIImage로 가져올 수도 있음*

4. FlatMap : trsnsforming operator

- **데이터를 stream으로 반환**하는 Operator
- 현재 데이터의 mapping이 끝나지 않았는데 다음 데이터가 넘어오면 반환되는 stream 내에서 중첩됨

5. Filter : filtering operator

- `.filter`는 True값만 통과시켜주는 Operator
- filter도 **기존에 존재하는 stream에 연결하는 Operator**

6. First : filtering operator

- 기존 stream 데이터에선 첫번째 항목만 받고 complete되는 Operator
- 파생된 Operator들은 Marble Diagram을 통해 이해하고 상황에 맞게 사용하면 됨

7. Concat : Mathematical and Aggregate Operator

- 여러개의 Observable을 받으면 첫번째 Observable이 complete되는 동시에 두번째 Observable을 연결시키면서 모든 Observable을 하나의 stream으로 반환하는 Operator

### Operator 처리 : .subscribe

- 모든 Operators를 거친 최종 데이터를 받는 method
- return 타입이 Disposable(DisposeBag에 담아서 처리)
- 최종 데이터를 받고 사용하려면 `.subscribe(on: EventType)` (EventType은 EnumType)
  - .next : 데이터 전달할 때 호출  
  - .eroor : 오류가 났들 때 호출  
  - .completed : 모든 step이 다 끝났을 때 호출  
  - **error가 나거나 completed되면 DisposeBag에서도 사라짐**
- `.subscribe(on:)`과 비슷한 method로, `.subscribe(onNext:,onError:,onCompleted:,onDisposed:)`
  - onNext 호출되었을 때 : onCopleted, onDisposed 호출  
  - onError 호출되었을 때 : onDisposed 호출  
  - onDisposed가 호출되는 경우 : complete되거는 경우, error나는 경우 등

### Scheduler

- 주어진 작업들을 스케쥴링하는 작업
- Async하게 작업하기 위해 multi-thread로 스케쥴링

  #### 멀티스레딩 핵심 method : Main Thread <-> Concurrence Thread
  
  1. observeOn()
  
  - observeOn()의 아래줄부터 적용됨
  - `.observeOn(ConcurrentDispatchQueueScheduler(qos:))` : Main Thread에서 동작중인 작업을 Concurrence Thread에서 동작시킴 (*qos는 우선순위로, 주로 .default 사용*)
  - `.observeOn(MainScheduler.instance)` : Concurrence Thread에서 동작중인 쟉업을 Main Thread에서 동작시킴

  2. subscribeOn() : 
  
  - .subscribe를 쓰는 경우는 무조건 첫줄부터 적용됨
  - .subscireOn()은 어느 줄에 작성하던 순서 상관없이 첫줄부터 적용됨
  - `.subscribeOn(ConcurrentDispathQueueScheduler(qos:)))`

### Subject

- Observer, Observable 모두로 동작 가능 즉, **데이터를 넣어줄 수도 있고 subscribe도 할 수 있음**

1. PublishSubject

- subscribe하면 데이터값을 방출하다가, 다음 subscribe가 일어나면 계속해서 방출되던 데이터가 방출됨(별다른 행동이 없음)

2. BehaviorSubject

- subscribe하면 초기값(default값)으 받고 다음 subscribe 전까지는 원래 Observable 내에 있던 데이터를 방출함
- 다음 subscribe이 발생되면 가장 최근 데이터값을 방출함
- Error가 났을 경우, 다음 subscribe한 Observable 모두에게 error값을 방출함(가장 최근값이 error값이기 때문)

3. ReplaySubject

- subscribe하면 처음엔 PublishSubject처럼 데이터를 방출하고, 다음 subscribe가 일어나면 지금까지 방출된 데이터를 모두 방출하고 계속해서 방출함

4. AsyncSubject

- subscribe를 해도 현재 Subject가 모두 끝나고나서 마지막 데이터만을 방출함
- Error가 나면, error값만을 방출함

### Side-Effect

- { }밖의 외부에 영향을 주는 동작
- side-effect를 허용해주는 곳
  - .subscribe()
  - .do() : 특정 이벤트가 발생했을 때 콜백해주는 method, 요소를 수정하진 않고 전달만 함

### RxCocoa
: 등록은 podfile에 `pod 'RxCocoa'`

- UIKit의 요소들을 다룰 때 좋을만한 extension들이 추가로 있음
- `.bind`를 통해 
