## ReactiveX
### observabal streams으로 Async 프로그래밍을 하기 위한 API
### 즉, Swift로 Reactive 프로그래밍을 하기 위한 라이브러리

---

### Sync/Async

- Sync(동기) : 작업을 하면 다른 작업 못하게 막음
- Async(비동기) : 작업을 해도 다른 작업은 계속 실행됨

> **Sync를 Async로 하려면 DispatchQueue하면 됨**
>
> `DispatchQueue.global().async`은 concurrence(동시 발생)을 async방식으로 하는 것
>
> `DispatchQueue.main.async`은 main thread로 돌리는 것

### RxSwift

- Async 코드를 간결하게 쓰기위해 사용함
- RxSwift install하려면 pod file에 `pod 'RxSwift`만 명시해주면 됨
- PromiseKit과 비슷하지만, return 타입이 Observable타입
- return할 때, Observabal을 create하고 seal in에 넣어 `.onNext`로 보냄

### Observable

- Observable : 하나 또는 연속된 항목을 배출
- Observer는 Observable을 구독하고, Observable은 항목을 배출하거나 Observable의 메서드 호출을 통해 Observer에게 알람을 보냄

### Disposable

- Observable에서 `.subscribe`하면 return되는 타입
- Disposable을 통해 dispose하면 작업 취소됨
- error가 나거나 특정상황일 때 모든 작업을 취소하기 위해 사용
- RxSwift나 PromiseKit을 사용하지 않고 dipose하려면 OperationQueue와 귀찮은 작업들을 해야함

### DisposeBag : dispose를 담는 가방

- DisposeBag에 insert 되어있는 작업을 한번에 dispose해줌
- dipose한 DisposeBag 안의 Disposable들은 없어짐

### Operators

1. just : creating operator

- Observabal에서 create해서 seal in에 넣어서 내려오는 것과 같은 기능
- Observabal.just의 문자열이 `.subscribe`할 때 첫번째 인자로 그대로 내려옴
- 문자열이 아니라 Array여도 그대로 내려옴

2. From : creating operator

- Array에 있는 요소들을 하나씩 떼어서 내려줌(Array 요소 타입은 Any)

3. Map : transforming operator

- `.just` 문자열이 `.map`을 먼저 거치면서 문자열 뒤에 "RxSwift"를 추가시키고, 바뀐(mapping된) 문자열이 내려감
- Array인 경우, Array를 from을 거쳐 내려보내면서 mapping되면 `.count`를 시켜 Int타입으로 바뀌고 값이 내려감
*예시) mapping을 연속해서 사용할 경우, 링크를 URL객체로 변환시키고 data를 가져와서 UIImage로 가져올 수도 있음*

4. Filter : filtering operator

- `.filter`는 True값만 통과

